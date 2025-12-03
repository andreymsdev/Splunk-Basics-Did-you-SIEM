# Splunk Basics — Did you SIEM?  

Neste desafio do *Advent of Cyber* da TryHackMe, utilizei o Splunk para investigar um ataque simulado contra um servidor web. O objetivo foi praticar análise de logs, threat hunting e habilidades em SIEM, descobrindo como o atacante comprometeu o sistema e quais dados foram exfiltrados.  

---

## Perguntas a responder
- Qual é o IP do atacante que comprometeu o servidor web?  
- Qual foi o dia de pico de tráfego nos logs? (Formato: YYYY-MM-DD)  
- Qual é a contagem de eventos com user_agent Havij nos logs?  
- Quantas tentativas de path traversal para acessar arquivos sensíveis foram observadas?  
- Ao examinar os firewall logs, quantos bytes foram transferidos para o servidor C2 a partir do servidor comprometido?  

---

## Passo 1: Explorando os Logs  
**Pergunta:** Qual é o IP do atacante?  
Comecei com uma consulta simples:  
```spl
index=main sourcetype=web_traffic
```  
Isso me permitiu explorar os logs ingeridos e identificar o IP suspeito do cliente.  

---

## Passo 2: Detectando Tráfego nos Logs  
**Pergunta:** Qual foi o dia de pico de tráfego?  
Aqui foquei no campo `client_ip` e usei visualizações para analisar os padrões de tráfego.  
Ao plotar os dados, ficou claro qual dia teve o maior pico de atividade.  

---

## Passo 3: SQL Injection  
**Pergunta:** Qual é a contagem de eventos Havij?  
Filtrando pelo user agent Havij, verifiquei os valores principais e rapidamente identifiquei o número de eventos associados a tentativas automatizadas de SQL injection.  

---

## Passo 4: Tentativas de Path Traversal  
**Pergunta:** Quantas tentativas de path traversal foram observadas?  
Usando o IP do atacante e filtrando caminhos suspeitos, consegui contar os eventos que mostravam tentativas de acessar arquivos sensíveis via path traversal.  

---

## Passo 5: Firewall Logs e Exfiltração  
**Pergunta:** Quantos bytes foram transferidos para o servidor C2?  
Embora eu não tenha capturado um print aqui, a consulta foi:  
```spl
sourcetype=firewall_logs src_ip=<compromised_server> dest_ip=<attacker_ip>
```  
Isso confirmou que as conexões foram permitidas para o servidor C2. Finalmente, usei:  
```spl
| stats sum(size_bytes) AS total_exfiltrated
```  
para calcular o total de dados exfiltrados.  

---

## O que eu aprendi  
- **Fundamentos do Splunk**: como usar `index`, `sourcetype` e filtros de campos para explorar logs de forma eficaz.  
- **Habilidades de visualização**: utilizando `timechart` e outros comandos para identificar anomalias e dias de pico de tráfego.  
- **Mentalidade de threat hunting**: filtrando tráfego benigno (navegadores comuns) para focar em user agents suspeitos como sqlmap, Havij, curl e wget.  
- **Análise da cadeia de ataque**: rastreando os passos do atacante desde o reconhecimento (procurando arquivos de configuração), exploração (SQL injection, path traversal), até a exfiltração e comunicação com o C2.  
- **Valor do SIEM**: percebendo como o Splunk pode correlacionar tráfego web com logs de firewall para confirmar exfiltração de dados e infraestrutura do atacante.  

No geral, este exercício reforçou como ferramentas de SIEM são poderosas para detectar, investigar e responder a incidentes de segurança. Foi uma forma prática de exercitar análise de logs, detecção de anomalias e fluxos de resposta a incidentes.  

---

Para mais: https://medium.com/@andreymsdev/splunk-basics-did-you-siem-cb53cf00cd96
