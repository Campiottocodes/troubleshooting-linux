# 📘 Playbook – Falha de Sincronização NTP (Chrony)

## 📍 Objetivo
Restabelecer a sincronização de horário do sistema em servidores Linux utilizando Chrony.

## 🚨 Sintoma
Servidor apresenta horário incorreto ou não sincronizado via NTP.

## 🔎 Diagnóstico

Executar:

```bash
chronyc tracking
chronyc sources -v
timedatectl
```

### Validar
- `Leap status: Not synchronised`
- `NTP synchronized: no`
- Fonte com status `^?`
- `reach 0`

## 🌐 Validação de Rede

### 1. Testar o servidor NTP configurado

```bash
ping -c 4 <IP_NTP>
```

Se houver `100% packet loss`, o servidor NTP está inacessível.

### 2. Testar saída externa

```bash
ping -c 4 8.8.8.8
```

Se responder, a rede externa está funcional.  
Se não responder, investigar conectividade, rota, NAT ou firewall.

## ⚙️ Análise de Configuração

```bash
cat /etc/chrony.conf | grep -E "server|pool"
```

Verificar:
- Se existe apenas um servidor configurado
- Se o servidor é interno
- Se ele está acessível

## 🛠️ Correção

Editar o arquivo de configuração:

```bash
sudo nano /etc/chrony.conf
```

Adicionar uma fonte pública:

```bash
pool pool.ntp.org iburst
```

Reiniciar o serviço:

```bash
sudo systemctl restart chronyd
```

## ✅ Validação

Executar novamente:

```bash
chronyc sources -v
```

Esperado:
- Presença de `^*`, indicando sincronização ativa

Depois validar com:

```bash
timedatectl
```

Esperado:
- `NTP synchronized: yes`

## 📊 Causa Raiz
Dependência de um único servidor NTP indisponível (SPOF).

## 🧠 Observações Operacionais
- `^?` indica ausência de resposta
- `^*` indica sincronização ativa
- Sempre utilizar múltiplas fontes NTP
- Validar rede antes de alterar configuração

---

# ⚡ Checklist Rápido (Execução)

## 1. Verificar status NTP

```bash
chronyc tracking
chronyc sources -v
timedatectl
```

Prosseguir se houver:
- `Not synchronised`
- `^?`
- `reach 0`

---

## 2. Testar conectividade NTP

```bash
ping -c 4 <IP_NTP>
```

Sem resposta → problema no servidor NTP

---

## 3. Testar saída externa

```bash
ping -c 4 8.8.8.8
```

Responde → rede OK  
Não responde → problema de rede

---

## 4. Verificar configuração

```bash
cat /etc/chrony.conf | grep -E "server|pool"
```

Apenas servidor interno → risco de falha

---

## 5. Corrigir

```bash
sudo nano /etc/chrony.conf
```

Adicionar:

```bash
pool pool.ntp.org iburst
```

Reiniciar:

```bash
sudo systemctl restart chronyd
```

---

## 6. Validar

```bash
chronyc sources -v
```

Deve aparecer:
- `^*`

```bash
timedatectl
```

Deve aparecer:
- `NTP synchronized: yes`
