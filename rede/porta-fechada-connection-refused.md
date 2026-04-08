# 📘 Playbook – Porta TCP fechada (Connection Refused)

## 📍 Objetivo
Validar disponibilidade de serviço em uma porta específica e identificar causa de falha na conexão.

## 🚨 Sintoma
Falha ao tentar conectar em uma porta específica via SSH.

Erro apresentado:
- `connection refused`

---

## 🔎 Diagnóstico

Executar:

```bash
ping <IP_DESTINO>
nmap -p <PORTA_SERVICO> <IP_DESTINO>
ssh -p <PORTA_SERVICO> usuario@<IP_DESTINO>
```

---

## 🌐 Validação de Rede

### Teste de conectividade

```bash
ping <IP_DESTINO>
```

Resultado esperado:
- Host responde normalmente

✔ Interpretação:
- O host está acessível na rede
- Não é problema de conectividade

---

## 🔍 Verificação da Porta

```bash
nmap -p <PORTA_SERVICO> <IP_DESTINO>
```

Resultado:
- `STATE: closed`

✔ Interpretação:
- A porta está acessível, mas sem serviço escutando

---

## 🛠️ Teste de Conexão

```bash
ssh -p <PORTA_SERVICO> usuario@<IP_DESTINO>
```

Resultado:
- `connection refused`

✔ Interpretação:
- A conexão chegou até o host
- O host recusou a conexão
- Não há serviço ativo na porta

---

## 📊 Causa Raiz
A porta `<PORTA_SERVICO>` está fechada no host de destino, indicando que não há serviço escutando nela ou o serviço não está configurado para utilizar essa porta.

---

## 🧠 Observações Operacionais

- `connection refused` → host respondeu, mas recusou a conexão
- Diferença importante:
  - `closed` → sem serviço escutando
  - `filtered` → possível firewall bloqueando
- Ping funcionando não garante porta aberta
- SSH por padrão utiliza porta 22

---

# ⚡ Checklist Rápido (Execução)

## 1. Validar conectividade

```bash
ping <IP_DESTINO>
```

Se responder → continuar

---

## 2. Verificar porta

```bash
nmap -p <PORTA_SERVICO> <IP_DESTINO>
```

Se `closed` → não há serviço ativo

---

## 3. Testar conexão

```bash
ssh -p <PORTA_SERVICO> usuario@<IP_DESTINO>
```

Se `connection refused` → conexão recusada pelo host

---

## 4. Conclusão

- Host acessível
- Porta fechada
- Serviço não está em execução ou não está configurado na porta informada
