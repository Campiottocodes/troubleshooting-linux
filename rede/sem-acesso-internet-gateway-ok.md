# 📘 Playbook – Falha de saída de rede (sem acesso externo)

## 📍 Objetivo
Identificar e diagnosticar falha de conectividade externa em servidor Linux, impactando serviços como NTP e instalação de pacotes.

## 🚨 Sintoma
- Falha ao sincronizar horário via NTP
- Falha ao instalar pacotes via gerenciador (yum)
- Erros ao acessar repositórios externos

---

## 🔎 Diagnóstico

Executar:

```bash
ping -c 4 8.8.8.8
ip route
ping -c 4 <GATEWAY>
traceroute 8.8.8.8
timedatectl
```

---

## 🌐 Validação de Rede

### Teste de conectividade externa

```bash
ping -c 4 8.8.8.8
```

Resultado:
- 100% packet loss

✔ Interpretação:
- Sem acesso à internet

---

### Teste de conectividade com gateway

```bash
ping -c 4 <GATEWAY>
```

Resultado:
- 0% packet loss

✔ Interpretação:
- Rede local funcional
- Comunicação com gateway OK

---

### Análise de rota

```bash
ip route
```

Resultado:
- Presença de rota default via gateway

✔ Interpretação:
- Rota configurada corretamente
- Problema não é ausência de rota

---

### Teste de caminho (traceroute)

```bash
traceroute 8.8.8.8
```

Resultado:
- Interrupção no primeiro salto (`* * *`)

✔ Interpretação:
- Tráfego não sai da rede local
- Bloqueio ou falha após o gateway

---

## ⚙️ Validação de Serviços Impactados

### NTP

```bash
timedatectl
```

Resultado:
- `NTP synchronized: yes`

✔ Interpretação:
- Status inconsistente
- Não reflete conectividade real com servidores externos

---

### Gerenciador de pacotes

```bash
yum update
yum install chrony
```

Resultado:
- Falha ao acessar mirrors
- Mensagens de tentativa de outros espelhos

✔ Interpretação:
- Sem acesso a repositórios externos

---

## 📊 Causa Raiz
Ausência de saída de rede além do gateway, impedindo comunicação com a internet.

Possíveis causas:
- Falha de NAT
- Bloqueio de firewall
- Problema de roteamento na borda da rede

---

## 🧠 Observações Operacionais

- Ping no gateway não garante acesso externo
- `traceroute` parando no primeiro salto indica bloqueio fora do host
- Serviços dependentes de internet (NTP, yum) falham em cascata
- `timedatectl` pode mostrar estado inconsistente

---

# ⚡ Checklist Rápido (Execução)

## 1. Testar internet

```bash
ping -c 4 8.8.8.8
```

Sem resposta → problema externo

---

## 2. Testar gateway

```bash
ping -c 4 <GATEWAY>
```

Responde → rede local OK

---

## 3. Verificar rota

```bash
ip route
```

Default presente → rota OK

---

## 4. Testar caminho

```bash
traceroute 8.8.8.8
```

Para no primeiro salto → bloqueio na saída

---

## 5. Validar impacto

```bash
yum update
timedatectl
```

Falhas confirmam ausência de internet

---

## 6. Conclusão

- Host comunica com gateway
- Sem saída para internet
- Problema fora do host (rede/NAT/firewall)
