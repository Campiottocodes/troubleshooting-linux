---

# ⚡ Checklist Rápido (Execução)

## 1. Verificar status NTP

chronyc tracking
chronyc sources -v
timedatectl


✔ Se:
- Not synchronised
- ^?
- reach 0

→ Prosseguir

---

## 2. Testar conectividade NTP

ping -c 4 <IP_NTP>


❌ Sem resposta → problema no servidor NTP

---

## 3. Testar saída externa

ping -c 4 8.8.8.8

 
✔ Responde → rede OK  
❌ Não responde → problema de rede

---

## 4. Verificar configuração

cat /etc/chrony.conf | grep -E "server|pool"


✔ Apenas servidor interno → risco de falha

---

## 5. Corrigir

sudo nano /etc/chrony.conf


Adicionar:

pool pool.ntp.org iburst


Reiniciar:

sudo systemctl restart chronyd


---

## 6. Validar

chronyc sources -v


✔ Deve aparecer: ^*


timedatectl


✔ NTP synchronized: yes
