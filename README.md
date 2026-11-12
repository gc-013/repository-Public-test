<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="robots" content="noindex,nofollow">
<title>LINE 警報接收者登記</title>
<style>
:root{font-family:"Microsoft JhengHei","Noto Sans TC",system-ui,sans-serif;color:#19364e;background:#f1f7fa}
*{box-sizing:border-box}body{margin:0;padding:24px 14px}main{max-width:620px;margin:auto;background:white;border-radius:18px;padding:26px;box-shadow:0 8px 32px #15395813}
small{color:#677a88}h1{font-size:clamp(23px,5vw,31px);line-height:1.35}p{line-height:1.8}#result{padding:18px;background:#f1f8fb;border-radius:13px}
.id{overflow-wrap:anywhere;border:1px solid #cbdce6;background:#fff;border-radius:9px;padding:16px;font-size:16px;font-family:monospace}
button{width:100%;border:none;background:#1573b7;color:white;border-radius:10px;font-size:18px;font-weight:bold;padding:15px;margin-top:15px;cursor:pointer}
#status{font-weight:700;color:#2c6679}.notice{margin-top:20px;padding:13px;border-left:4px solid #0b8d88;background:#f2fbfa}
[hidden]{display:none!important}a{color:#1270aa}
</style>
<script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
</head>
<body><main>
<small id="brand"></small>
<h1>LINE 漏水警報接收者登記</h1>
<p>請使用欲接收通知的個人 LINE 帳號登入。取得 User ID 後交由設備管理員審核；完成審核並設定接收範圍後，才會開始收到通知。</p>
<div id="status" role="status" aria-live="polite">正在載入 LINE 身分驗證…</div>
<section id="result" hidden>
  <p id="displayName"></p><p>個人 LINE User ID：</p><div id="userId" class="id"></div>
  <button id="copyBtn" type="button">複製 User ID</button><p id="copyMsg" aria-live="polite"></p>
</section>
<button id="loginBtn" hidden type="button">使用個人 LINE 登入</button>
<button id="retryBtn" hidden type="button">重新嘗試</button>
<div class="notice">取得 User ID ≠ 已有警報接收權限。須由客戶 ADMIN 登記、核准、指定 WLS 並完成單人測試。請先加入客戶 LINE 官方帳號好友，確認未封鎖。</div>
<p id="oa" hidden><a id="oaLink" rel="noopener noreferrer" target="_blank">加入 LINE 官方帳號好友</a></p>
<small>本頁不將 User ID 自動傳送至 GitHub、冠群或其他伺服器；使用者自行交付給所屬設備管理員。</small>
</main>
<script>
// ================== 客戶必改設定（請勿填入任何 Token 或 Secret）==================
const CONFIG={
  LIFF_ID:'2012560689-Rxd5qDaA', // 必改，例如 1234567890-AbCdEfGh
  SERVICE_NAME:'GC',          // 必改，頁面上方識別
  OA_FRIEND_URL:''                         // 選填，https://lin.ee/xxxxxx（OA 好友連結）
};
// ================================================================================
const $=s=>document.getElementById(s);
$('brand').textContent=CONFIG.SERVICE_NAME;
if(CONFIG.OA_FRIEND_URL){$('oa').hidden=false;$('oaLink').href=CONFIG.OA_FRIEND_URL;}
let currentId='';
function status(s){$('status').textContent=s;}
async function launch(){
  $('result').hidden=true;$('loginBtn').hidden=true;$('retryBtn').hidden=true;
  if(CONFIG.LIFF_ID.startsWith('REPLACE_')){status('尚未替換客戶 LIFF ID；請聯絡建置管理員。');return;}
  if(!window.liff){status('LINE SDK 無法載入，請檢查網路。');$('retryBtn').hidden=false;return;}
  try{
    status('正在連線 LINE…');
    await liff.init({liffId:CONFIG.LIFF_ID});
    if(!liff.isLoggedIn()){
      status('請以欲接收警報的個人 LINE 帳號登入。');$('loginBtn').hidden=false;return;
    }
    const profile=await liff.getProfile();
    if(!profile || !/^U[0-9a-f]{32}$/i.test(profile.userId||''))throw Error('LINE 未提供有效的 User ID，請檢查 profile 權限');
    currentId=profile.userId;
    $('displayName').textContent='LINE 顯示名稱：'+(profile.displayName||'未提供');
    $('userId').textContent=currentId;
    $('result').hidden=false;
    status('LINE 身分驗證成功');
  }catch(e){status('無法取得 User ID：'+(e?.message||String(e)));$('retryBtn').hidden=false;}
}
$('loginBtn').addEventListener('click',()=>liff.login({redirectUri:location.origin+location.pathname}));
$('retryBtn').addEventListener('click',launch);
$('copyBtn').addEventListener('click',async()=>{
  if(!currentId)return;
  try{
    if(navigator.clipboard?.writeText)await navigator.clipboard.writeText(currentId);
    else{const a=document.createElement('textarea');a.value=currentId;a.style.position='fixed';a.style.opacity='0';document.body.appendChild(a);a.select();if(!document.execCommand('copy'))throw Error('copy fail');a.remove();}
    $('copyMsg').textContent='已複製，請提供給設備管理員審核。';
  }catch(e){$('copyMsg').textContent='無法自動複製，請手動選取上方 User ID。';}
});
launch();
</script>
</body></html>
