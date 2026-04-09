# 📘 Playbook – NTP não sincroniza após alteração de timezone

## 📍 Objetivo
Diagnosticar falha de sincronização NTP após alteração de timezone em sistema Linux.

## 🚨 Sintoma
- Horário incorreto após alteração de timezone
- Diferença de -1h no sistema
- NTP não sincronizando

---

## 🔎 Diagnóstico

Executar:

```bash
timedatectl
date
date -u
```

---

### Validar timezone e sincronização

```bash
timedatectl
```

Resultado:
- Time zone alterado
- `NTP synchronized: no`

---

### Validar horário do sistema

```bash
date
date -u
```

Resultado:
- Diferença de horário (-1h)

---

## ⚙️ Verificação do serviço NTP

```bash
systemctl status chronyd
systemctl status systemd-timesyncd
```

Resultado:
- chronyd ativo
- systemd-timesyncd inexistente

✔ Interpretação:
- Serviço NTP está rodando, mas não sincronizando

---

## 🔍 Análise do Chrony

```bash
chronyc tracking
chronyc sources -v
```

Resultado:
- `Leap status: not synchronised`
- `^?`
- `Reach = 0`
- `LastRx = 0`

✔ Interpretação:
- Sem comunicação com servidores NTP

---

## 🌐 Validação de Rede

```bash
ping -c 3 8.8.8.8
```

Resultado:
- 100% packet loss

✔ Interpretação:
- Sem acesso à internet
- NTP não consegue sincronizar

---

## ⚠️ Observação sobre timezone

```bash
timedatectl set-timezone America/Manaus
```

✔ Interpretação:
- Alteração de timezone não causa falha de NTP
- Porém expõe problema de sincronização quando o sistema precisa atualizar horário

---

## 🛠️ Ação Realizada

```bash
timedatectl set-ntp false
date HH:MM:SS
hwclock --systohc
```

✔ Resultado:
- Ajuste manual do horário
- Sincronização com relógio de hardware

---

## 📊 Causa Raiz
Ausência de conectividade de rede, impedindo comunicação com servidores NTP.

A alteração de timezone removeu a dependência do estado anterior em cache, exigindo nova sincronização, que falhou devido à falta de acesso externo.

---

## 🧠 Observações Operacionais

- Alterar timezone não quebra NTP
- NTP depende de conectividade externa
- `^?` + `Reach 0` = sem comunicação
- `timedatectl` pode indicar estado inconsistente
- Ajuste manual é apenas solução temporária

---

# ⚡ Checklist Rápido (Execução)

## 1. Verificar timezone e NTP

```bash
timedatectl
```

Se:
- `NTP synchronized: no` → continuar

---

## 2. Verificar serviço

```bash
systemctl status chronyd
```

Se ativo → continuar

---

## 3. Verificar sincronização

```bash
chronyc sources -v
```

Se:
- `^?`
- `Reach 0`

→ sem comunicação com NTP

---

## 4. Testar rede

```bash
ping -c 3 8.8.8.8
```

Sem resposta → problema de rede

---

## 5. Mitigação temporária

```bash
timedatectl set-ntp false
date HH:MM:SS
hwclock --systohc
```

---

## 6. Conclusão

- NTP não sincroniza
- Causa: ausência de conectividade externa
- Timezone apenas evidenciou o problema