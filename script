import requests
import json

# IČO pre iGrow Network, s.r.o.
ico = "50394860"

# 1. Získanie ID účtovnej jednotky z RÚZ
url_uj = f"https://registeruz.sk{ico}"
response_uj = requests.get(url_uj)

html_content = f"""
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Dáta z RÚZ - {ico}</title>
    <style>
        body {{ font-family: Arial, sans-serif; margin: 40px; background-color: #f4f6f9; }}
        h1, h2 {{ color: #333; }}
        table {{ border-collapse: collapse; width: 100%; background: white; margin-bottom: 30px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }}
        th, td {{ border: 1px solid #dddddd; text-align: left; padding: 12px; }}
        th {{ background-color: #007bff; color: white; }}
        tr:nth-child(even) {{ background-color: #f9f9f9; }}
    </style>
</head>
<body>
    <h1>Finančné údaje z Registra účtovných závierok</h1>
    <p>Vygenerované automaticky cez GitHub Actions.</p>
"""

if response_uj.status_code == 200 and response_uj.json().get('id'):
    id_uj = response_uj.json()['id'][-1] # Vezmeme najnovšie ID
    
    # 2. Načítanie detailov o firme
    url_detail = f"https://registeruz.sk{id_uj}"
    data = requests.get(url_detail).json()
    
    # Prvá tabuľka: Základné info
    html_content += f"""
    <h2>Základné informácie o subjekte</h2>
    <table>
        <tr><th>Názov firmy</th><td>{data.get('obchodneMeno', 'N/A')}</td></tr>
        <tr><th>IČO</th><td>{data.get('ico', 'N/A')}</td></tr>
        <tr><th>Sídlo</th><td>{data.get('mesto', '')}, {data.get('ulica', '')} {data.get('cisloPopisne', '')}</td></tr>
        <tr><th>Dátum založenia</th><td>{data.get('datumZalozenia', 'N/A')}</td></tr>
    </table>
    """
    
    # Druhá tabuľka: Zoznam podaných závierok
    html_content += """
    <h2>Zoznam účtovných závierok</h2>
    <table>
        <tr>
            <th>ID závierky</th>
            <th>Obdobie od</th>
            <th>Obdobie do</th>
            <th>Dátum zostavenia</th>
        </tr>
    """
    for zaviazka_id in data.get('idUctovnychZavierok', [])[-5:]: # Posledných 5 závierok
        url_zavierka = f"https://registeruz.sk{zaviazka_id}"
        z_data = requests.get(url_zavierka).json()
        html_content += f"""
        <tr>
            <td>{z_data.get('id', 'N/A')}</td>
            <td>{z_data.get('obdobieOd', 'N/A')}</td>
            <td>{z_data.get('obdobieDo', 'N/A')}</td>
            <td>{z_data.get('datumZostavenia', 'N/A')}</td>
        </tr>
        """
    html_content += "</table>"
else:
    html_content += "<p>Chyba: Nepodarilo sa načítať dáta pre toto IČO z API.</p>"

html_content += "</body></html>"

# Uloženie do HTML súboru
with open("index.html", "w", encoding="utf-8") as f:
    f.write(html_content)

print("HTML súbor index.html bol úspešne vytvorený!")
