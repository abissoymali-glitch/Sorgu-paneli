from flask import Flask, render_template_string, request
import os
import requests

app = Flask(__name__)

HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>Herkese Açık Sorgu Paneli</title>
    <style>
        body { background-color: #121212; color: #e0e0e0; font-family: Arial, sans-serif; padding: 20px; }
        .container { max-width: 600px; margin: auto; background: #1e1e1e; padding: 20px; border-radius: 8px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        h2 { text-align: center; color: #ff4757; }
        select, input, button { width: 100%; padding: 10px; margin: 10px 0; background: #2f2f2f; color: #fff; border: 1px solid #444; border-radius: 4px; box-sizing: border-box; }
        button { background: #ff4757; color: white; font-weight: bold; cursor: pointer; border: none; }
        button:hover { background: #ff6b81; }
        pre { background: #000; padding: 15px; border-radius: 4px; overflow-x: auto; color: #00ff00; white-space: pre-wrap; word-wrap: break-word; }
    </style>
</head>
<body>
    <div class="container">
        <h2>Sorgu Paneli</h2>
        <form method="POST">
            <label>Sorgu Türü Seç:</label>
            <select name="endpoint" id="endpoint" onchange="updateInputs()">
                <option value="tc">TC Sorgu</option>
                <option value="tcpro">TC Pro Sorgu</option>
                <option value="adsoyad">Ad Soyad Sorgu</option>
                <option value="aile">Aile Sorgu</option>
                <option value="ailepro">Aile Pro Sorgu</option>
                <option value="sulale">Sülale Sorgu</option>
                <option value="eokul">E-Okul Sorgu</option>
                <option value="tapu">Tapu Sorgu</option>
                <option value="adaparsel">Ada Parsel Sorgu</option>
                <option value="gsmtc">GSM-TC Sorgu</option>
                <option value="tcgsm">TC-GSM Sorgu</option>
                <option value="mernis_soyisim">MERNIS Soyisim Sorgu</option>
                <option value="mernis">MERNIS TC Sorgu</option>
                <option value="plaka">Plaka Sorgu</option>
                <option value="ipinfo">IP Sorgu</option>
            </select>

            <div id="input-fields"></div>

            <button type="submit">Sorgula</button>
        </form>

        {% if result %}
            <h3>Sonuç:</h3>
            <pre>{{ result }}</pre>
        {% endif %}
    </div>

    <script>
        function updateInputs() {
            let ep = document.getElementById("endpoint").value;
            let container = document.getElementById("input-fields");
            let html = "";

            if (['tc', 'tcpro', 'aile', 'ailepro', 'sulale', 'eokul', 'tapu', 'mernis', 'tcgsm'].includes(ep)) {
                html = '<input type="text" name="param1" placeholder="TC Kimlik No" required>';
            } else if (ep === 'adsoyad') {
                html = '<input type="text" name="param1" placeholder="Ad" required><input type="text" name="param2" placeholder="Soyad" required>';
            } else if (ep === 'adaparsel') {
                html = '<input type="text" name="param1" placeholder="İl (Örn: İSTANBUL)" required><input type="text" name="param2" placeholder="İlçe (Örn: MALTEPE)" required>';
            } else if (ep === 'gsmtc') {
                html = '<input type="text" name="param1" placeholder="GSM (555...)" required><input type="text" name="param2" placeholder="Auth (fire)" value="fire" required>';
            } else if (ep === 'mernis_soyisim') {
                html = '<input type="text" name="param1" placeholder="Soyisim" required>';
            } else if (ep === 'plaka') {
                html = '<input type="text" name="param1" placeholder="Plaka (Örn: 34KG4978)" required>';
            } else if (ep === 'ipinfo') {
                html = '<input type="text" name="param1" placeholder="IP Adresi" required>';
            }
            container.innerHTML = html;
        }
        window.onload = updateInputs;
    </script>
</body>
</html>
"""

@app.route("/", methods=["GET", "POST"])
def index():
    result = None
    if request.method == "POST":
        endpoint = request.form.get("endpoint")
        p1 = request.form.get("param1", "")
        p2 = request.form.get("param2", "")
        
        base_url = "https://infolanmam.pro/api.php"
        url = ""
        
        if endpoint in ['tc', 'tcpro', 'aile', 'ailepro', 'sulale', 'eokul', 'tapu', 'mernis', 'tcgsm']:
            url = f"{base_url}?endpoint={endpoint}&tc={p1}"
        elif endpoint == 'adsoyad':
            url = f"{base_url}?endpoint=adsoyad&ad={p1}&soyad={p2}"
        elif endpoint == 'adaparsel':
            url = f"{base_url}?endpoint=adaparsel&il={p1}&ilce={p2}"
        elif endpoint == 'gsmtc':
            url = f"{base_url}?endpoint=gsmtc&gsm={p1}&auth={p2}"
        elif endpoint == 'mernis_soyisim':
            url = f"{base_url}?endpoint=mernis_soyisim&soyisim={p1}"
        elif endpoint == 'plaka':
            url = f"{base_url}?endpoint=plaka&plate={p1}"
        elif endpoint == 'ipinfo':
            url = f"{base_url}?endpoint=ipinfo&ip={p1}"
            
        try:
            response = requests.get(url, timeout=10)
            result = response.text
        except Exception as e:
            result = f"Bağlantı hatası: {str(e)}"
            
    return render_template_string(HTML_TEMPLATE, result=result)

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    app.run(host="0.0.0.0", port=port)
