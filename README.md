<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>iPhone 專業資產鑑定報告</title>
    <style>
        :root { --apple-blue: #0071e3; --bg-card: #1c1c1e; --text-gray: #86868b; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: #000; color: #fff; padding: 20px; margin: 0; display: flex; justify-content: center; }
        .box { width: 100%; max-width: 420px; }
        .t { font-size: 24px; font-weight: 600; text-align: center; margin-bottom: 25px; letter-spacing: -0.5px; }
        
        /* 表單樣式 */
        .card { background: var(--bg-card); padding: 20px; border-radius: 18px; margin-bottom: 20px; box-shadow: 0 4px 20px rgba(0,0,0,0.5); }
        label { display: block; font-size: 12px; color: var(--text-gray); margin-bottom: 8px; text-transform: uppercase; font-weight: 600; padding-left: 5px; }
        select, input { width: 100%; padding: 14px; background: #2c2c2e; border: none; border-radius: 12px; color: #fff; font-size: 16px; margin-bottom: 15px; box-sizing: border-box; outline: none; transition: 0.2s; }
        select:focus, input:focus { background: #3a3a3c; box-shadow: 0 0 0 2px var(--apple-blue); }
        .row { display: flex; gap: 12px; }
        
        /* 按鈕樣式 */
        #go { width: 100%; padding: 18px; background: var(--apple-blue); color: #fff; border: none; border-radius: 14px; font-size: 17px; font-weight: 600; cursor: pointer; transition: transform 0.1s; }
        #go:active { transform: scale(0.98); background: #0077ed; }

        /* 結果顯示區 */
        #res { display: none; margin-top: 25px; background: #fff; color: #000; border-radius: 20px; padding: 25px; animation: slideUp 0.4s ease-out; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .score-circle { width: 120px; height: 120px; border-radius: 50%; border: 8px solid var(--apple-blue); display: flex; align-items: center; justify-content: center; margin: 0 auto 15px; }
        .score { font-size: 42px; font-weight: 800; color: var(--apple-blue); }
        
        .adv-box { background: #f5f5f7; padding: 18px; border-radius: 14px; font-size: 15px; line-height: 1.6; color: #1d1d1f; margin-bottom: 15px; border-left: 5px solid var(--apple-blue); }
        .item { display: flex; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #e5e5e5; font-size: 14px; color: #48484a; }
        .item b { color: #000; font-size: 16px; }
        .price-highlight { color: var(--apple-blue) !important; font-size: 20px !important; }
    </style>
</head>
<body>

<div class="box">
    <div class="t">iPhone 資產價值精算</div>

    <div class="card">
        <label>設備型號</label>
        <select id="m">
            <optgroup label="iPhone 16 系列">
                <option value="16PM" data-p="44900">iPhone 16 Pro Max</option>
                <option value="16P" data-p="36900">iPhone 16 Pro</option>
                <option value="16PL" data-p="32900">iPhone 16 Plus</option>
                <option value="16" data-p="29900">iPhone 16</option>
            </optgroup>
            <optgroup label="iPhone 15 系列">
                <option value="15PM" data-p="44900">iPhone 15 Pro Max</option>
                <option value="15P" data-p="36900">iPhone 15 Pro</option>
                <option value="15" data-p="29900">iPhone 15</option>
            </optgroup>
            <optgroup label="iPhone 14 系列">
                <option value="14PM" data-p="38900">iPhone 14 Pro Max</option>
                <option value="14" data-p="27900">iPhone 14</option>
            </optgroup>
            <optgroup label="iPhone 11-13 系列">
                <option value="13" data-p="25900">iPhone 13 系列</option>
                <option value="12" data-p="26900">iPhone 12 系列</option>
                <option value="11" data-p="24900">iPhone 11 系列</option>
            </optgroup>
        </select>

        <div class="row">
            <div style="flex:1"><label>持有年數</label><input type="number" id="yr" value="2" step="0.5"></div>
            <div style="flex:1"><label>電池健康度 %</label><input type="number" id="ba" value="82"></div>
        </div>

        <label>後續用途</label>
        <select id="pu">
            <option value="f">送給家人使用</option>
            <option value="b">當備用機</option>
        </select>

        <label>現場鑑定回收價 (NT$)</label>
        <input type="number" id="pr" placeholder="請輸入金額" pattern="\d*">
        
        <button id="go" onclick="runCheck()">生成診斷報告</button>
    </div>

    <div id="res">
        <div class="score-circle" id="circle"><div class="score" id="sS">0%</div></div>
        <div style="text-align:center; font-size:12px; color:var(--text-gray); margin-bottom:20px; font-weight:600;">資產活化建議指數</div>
        
        <div class="adv-box" id="sA"></div>
        
        <div class="item"><span>預計領回現金</span><b id="sC" class="price-highlight"></b></div>
        <div class="item"><span>平均每月持有成本</span><b id="sM"></b></div>
    </div>
</div>

<script>
function runCheck() {
    var p = parseFloat(document.getElementById('pr').value);
    if(!p) { alert("請輸入金額"); return; }

    var yr = parseFloat(document.getElementById('yr').value) || 1;
    var mo = yr * 12; 
    var ba = parseInt(document.getElementById('ba').value);
    var sel = document.getElementById('m');
    var modelValue = sel.value; 
    var modelName = sel.options[sel.selectedIndex].text;
    var orig = parseFloat(sel.options[sel.selectedIndex].getAttribute('data-p'));
    var pu = document.getElementById('pu').value;
    
    var ratio = p / orig;
    var score = 0;
    var advice = "";
    var mainColor = "#0071e3";

    if (modelValue.indexOf('16') !== -1) {
        if (ratio < 0.4) { 
            score = 15; mainColor = "#ff3b30";
            advice = "<b>【價值評估預警】</b><br>目前的回收價因機況顯著低於資產價值。考慮到 16 系列效能仍屬頂尖，建議留用。";
        } else if (pu === 'f') {
            score = 92;
            advice = "<b>【高品質資產傳承】</b><br>" + modelName + " 效能卓越，若家中有使用需求，這是一項極優質的家庭資產配置。";
        } else {
            score = 85;
            advice = "<b>【備用機效益評估】</b><br>" + modelName + " 殘值尚高，當備用機將面臨每年數千元的跌價損失。建議現在回收，年年換新機。";
        }
    } 
    else if (modelValue.indexOf('14') !== -1 || modelValue.indexOf('15') !== -1) {
        if (ba < 80) {
            score = 95;
            advice = "<b>【效能衰退：建議結清】</b><br>電池健康度（" + ba + "%）已過低。現在領回 NT$ " + p.toLocaleString() + "，是避免資產與體驗雙輸的最佳選擇。";
        } else if (ba < 85) {
            if (pu === 'f') {
                score = 55; mainColor = "#ff9500";
                advice = "<b>【留用建議：代送維修】</b><br>若要留給家人，<b>建議由我們代送到授權中心更換電池</b>。請衡量維修費與等待時間，轉為新機補貼是否更具效益。";
            } else {
                score = 90;
                advice = "<b>【備用機警告】</b><br>電池已不穩定，長期存放易導致電池膨脹。建議趁現在仍有鑑定價值時回收套現。";
            }
        } else {
            score = 80;
            advice = "目前設備狀況良好。您可以選擇繼續使用，或趁二手需求高時回收，鎖定剩餘價值。";
        }
    } 
    else {
        score = 98;
        advice = "<b>【強力回收建議】</b><br>此機型已屆退役期。不論用途，現在領回 NT$ " + p.toLocaleString() + " 是最高效益。";
        if (pu === 'b') {
            advice += "<br>● <b>備用機效益低</b>：現今維修多可當日完修，舊機存放無實質意義，只會導致資產歸零。";
        } else {
            advice += "<br>● <b>送家人負擔大</b>：效能已不足，且更換電池需額外花費，建議直接變現補貼新機。";
        }
    }

    document.getElementById('sS').innerText = score + "%";
    document.getElementById('circle').style.borderColor = mainColor;
    document.getElementById('sS').style.color = mainColor;
    document.getElementById('sA').innerHTML = advice;
    document.getElementById('sC').innerText = "NT$ " + p.toLocaleString();
    document.getElementById('sM').innerText = "NT$ " + Math.floor((orig - p)/mo).toLocaleString() + " / 月";
    document.getElementById('res').style.display = "block";
}
</script>
</body>
</html>
