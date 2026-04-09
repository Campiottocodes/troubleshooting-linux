# 📘 Playbook – Chrony não sincroniza (sem resposta das fontes NTP)

## 📍 Objetivo
Diagnosticar falha de sincronização do serviço NTP (chronyd) quando não há resposta das fontes configuradas.

## 🚨 Sintoma
- Serviço NTP ativo, porém não sincronizando
- `Leap status: Not synchronized`
- Fontes NTP sem resposta (`^?`, reach 0)

---

## 🔎 Diagnóstico

Executar:

```bash
chronyc tracking
chronyc sources -v
chronyc activity
```

---

### Validar status do NTP

```bash
chronyc tracking
```

Resultado:
- `Leap status: Not synchronized`

---

### Validar fontes NTP

```bash
chronyc sources -v
```

Resultado:
- `^?`
- `stratum 0`
- `reach 0`
- `lastRx -`

✔ Interpretação:
- Nenhuma resposta das fontes NTP

---

### Verificar atividade

```bash
chronyc activity
```

Resultado:
- `1 sources online`

✔ Interpretação:
- Fonte configurada, porém sem resposta

---

## ⚙️ Verificação de Configuração

```bash
cat /etc/chrony/chrony.conf
```

Resultado inicial:
- Arquivo sem servidores configurados

---

### Correção de configuração

```bash
sudo nano /etc/chrony/chrony.conf
```

Adicionar:

```bash
server pool.ntp.org iburst
```

---

## 🌐 Validação de Rede

### Teste com gateway

```bash
ping -c 4 <GATEWAY>
```

Resultado:
- 0% packet loss

✔ Interpretação:
- Rede local funcional

---

### Teste de acesso externo

```bash
ping -c 4 8.8.8.8
```

Resultado:
- 100% packet loss

✔ Interpretação:
- Sem acesso à internet

---

### Verificação de rota

```bash
ip route
```

Resultado:
- Rota default via gateway presente

✔ Interpretação:
- Rota configurada
- Problema fora do host

---

## 🛠️ Ação Realizada

```bash
sudo nano /etc/chrony/chrony.conf
systemctl restart chronyd
timedatectl set-timezone America/Sao_Paulo
timedatectl
date
```

✔ Resultado:
- Serviço reiniciado
- Timezone ajustado
- Sem sincronização NTP devido à falta de conectividade

---

## 📊 Causa Raiz
Ausência de conectividade externa além do gateway, impedindo comunicação com servidores NTP.

Mesmo com configuração correta e serviço ativo, o chronyd não consegue sincronizar devido à falha de rede.

---

## 🧠 Observações Operacionais

- `^?` + `reach 0` = ausência de comunicação
- `sources online` não garante sincronização
- Configuração correta não resolve sem conectividade
- Ping no gateway não garante acesso externo
- NTP depende diretamente de rede

---

# ⚡ Checklist Rápido (Execução)

## 1. Verificar status NTP

```bash
chronyc tracking
```

Se:
- `Not synchronized` → continuar

---

## 2. Verificar fontes

```bash
chronyc sources -v
```

Se:
- `^?`
- `reach 0`

→ sem comunicação com NTP

---

## 3. Verificar configuração

```bash
cat /etc/chrony/chrony.conf
```

Se vazio → adicionar servidor

---

## 4. Testar rede

```bash
ping -c 4 8.8.8.8
```

Sem resposta → problema de rede

---

## 5. Validar rota

```bash
ip route
```

Default presente → problema fora do host

---

## 6. Conclusão

- Chrony configurado e ativo
- Sem sincronização
- Causa: ausência de conectividade externa