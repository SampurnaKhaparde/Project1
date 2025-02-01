# Project1

import smtplib
import pandas as pd
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.base import MIMEBase
from email import encoders


SMTP_SERVER = "smtp.gmail.com"
SMTP_PORT = 587
EMAIL_ADDRESS = "your_email@gmail.com" 
EMAIL_PASSWORD = "your_password"  
CSV_FILE = "contacts.csv" 


contacts = pd.read_csv(CSV_FILE)

def send_email(to_email, name, attachment_path):
    try:
        msg = MIMEMultipart()
        msg['From'] = EMAIL_ADDRESS
        msg['To'] = to_email
        msg['Subject'] = f"Hello {name}, Important Attachment Inside!"

        body = f"Dear {name},\n\nPlease find the attached file.\n\nBest regards,\nYour Company"
        msg.attach(MIMEText(body, 'plain'))


        if os.path.exists(attachment_path):
            with open(attachment_path, "rb") as attachment:
                part = MIMEBase('application', 'octet-stream')
                part.set_payload(attachment.read())
                encoders.encode_base64(part)
                part.add_header('Content-Disposition', f'attachment; filename={os.path.basename(attachment_path)}')
                msg.attach(part)
        
      
        server = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        server.starttls()
        server.login(EMAIL_ADDRESS, EMAIL_PASSWORD)
        server.sendmail(EMAIL_ADDRESS, to_email, msg.as_string())
        server.quit()

        print(f"Email sent to {name} ({to_email})")
    except Exception as e:
        print(f"Failed to send email to {name} ({to_email}): {e}")


for index, row in contacts.iterrows():
    send_email(row['email'], row['name'], row['attachment'])
