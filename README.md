<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Acceso con código — 4 dígitos</title>
  <style>
    body{font-family:Inter,system-ui,Segoe UI,Roboto,Helvetica,Arial,sans-serif;display:flex;align-items:center;justify-content:center;min-height:100vh;background:#f3f4f6;margin:0}
    .card{background:#fff;padding:28px;border-radius:12px;box-shadow:0 8px 24px rgba(15,23,42,0.08);width:360px;max-width:90%}
    h1{font-size:18px;margin:0 0 12px}
    p{margin:0 0 16px;color:#374151}
    input{width:100%;padding:12px;border-radius:8px;border:1px solid #e5e7eb;font-size:16px;box-sizing:border-box}
    button{margin-top:12px;width:100%;padding:10px;border-radius:8px;border:0;background:#111827;color:#fff;font-weight:600;cursor:pointer}
    .msg{margin-top:10px;font-size:14px}
    .success{color:green}
    .error{color:#b91c1c}
    .used-note{font-size:12px;color:#6b7280;margin-top:8px}
    .protected{padding:18px;background:#ecfeff;border-radius:8px;margin-top:12px}
    .link-box{margin-top:10px;padding:10px;background:#fff3cd;border:1px solid #ffeeba;border-radius:8px;font-size:14px}
  </style>
</head>
<body>
  <div class="card">
    <h1>Acceso con código (4 dígitos)</h1>
    <p>Introduce un código de 4 dígitos válido. Cada código solo puede usarse una vez.</p>
    <input id="codeInput" inputmode="numeric" pattern="[0-9]*" maxlength="4" placeholder="Ej: 1234" />
    <button id="btn">Entrar</button>
    <div class="msg" id="msg"></div>
    <div class="used-note">Los códigos usados se guardan en tu navegador (localStorage). Si deseas que funcione entre varios usuarios/sites necesitarás un servidor para validarlos y marcarlos como usados.</div>

    <div id="protectedContent" style="display:none">
      <div class="protected">
        <strong>Acceso concedido</strong>
        <p>Has accedido a la página protegida. Aquí puedes colocar el contenido que quieras mostrar solo a quienes tengan un código válido.</p>
        <div class="link-box">
          <strong>Link:</strong> <a href="https://www.haxball.com/play?c=pRTwBCk9CIU&p=1" target="_blank">https://www.haxball.com/play?c=pRTwBCk9CIU&p=1</a><br>
          <strong>Contraseña:</strong> GalaLirs
        </div>
      </div>
    </div>
  </div>

  <script>
    const validCodes = [
      '0232','1352','1425','1861','2043','2411','2484','2492','2772','2833',
      '2921','2984','3322','3732','3778','3993','4453','5087','5255','7238',
      '7254','7967','7982','8049','9876'
    ];

    const USED_KEY = 'acceso_4digitos_usados_v1';

    function getUsed(){
      try{
        const s = localStorage.getItem(USED_KEY);
        return s ? JSON.parse(s) : [];
      }catch(e){ return []; }
    }
    function saveUsed(arr){
      localStorage.setItem(USED_KEY, JSON.stringify(arr));
    }

    const input = document.getElementById('codeInput');
    const btn = document.getElementById('btn');
    const msg = document.getElementById('msg');
    const protectedContent = document.getElementById('protectedContent');

    function showMessage(text, kind){
      msg.textContent = text;
      msg.className = 'msg ' + (kind === 'ok' ? 'success' : 'error');
    }

    function normalize(code){
      return String(code).padStart(4,'0');
    }

    btn.addEventListener('click', tryCode);
    input.addEventListener('keydown', function(e){ if(e.key === 'Enter') tryCode(); });

    function tryCode(){
      const raw = input.value.trim();
      if(!/^[0-9]{4}$/.test(raw)){
        showMessage('Introduce un código válido de 4 dígitos.', 'err');
        return;
      }
      const code = normalize(raw);
      const used = getUsed();

      if(used.includes(code)){
        showMessage('Ese código ya fue usado.', 'err');
        return;
      }

      if(!validCodes.includes(code)){
        showMessage('Código inválido.', 'err');
        return;
      }

      used.push(code);
      saveUsed(used);
      showMessage('Código aceptado. Acceso concedido.', 'ok');
      input.value = '';
      protectedContent.style.display = 'block';
      btn.disabled = true;
      setTimeout(()=>btn.disabled=false, 800);
    }

    (function showRemaining(){
      const used = getUsed();
      const remaining = validCodes.filter(c=>!used.includes(c)).length;
      const el = document.createElement('div');
      el.style.marginTop='12px';
      el.style.fontSize='13px';
      el.style.color='#374151';
      el.textContent = `Códigos restantes: ${remaining} / ${validCodes.length}`;
      document.querySelector('.card').appendChild(el);
    })();
  </script>
</body>
</html>
