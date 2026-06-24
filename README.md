<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>টাকা ইনকাম মাস্টার</title>
    <style>
        body {
            background-color: #1a1e24;
            color: #ffffff;
            font-family: 'Arial', sans-serif;
            text-align: center;
            padding: 20px;
            margin: 0;
        }
        .container {
            max-width: 400px;
            margin: 0 auto;
            background: #252b35;
            padding: 25px;
            border-radius: 20px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        h1 { color: #4caf50; font-size: 24px; margin-bottom: 5px; }
        .subtitle { color: #cccccc; font-size: 16px; margin-bottom: 25px; }
        .balance-box {
            background: #323a48;
            padding: 15px;
            border-radius: 12px;
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 25px;
        }
        .btn {
            display: block;
            width: 100%;
            padding: 14px;
            margin: 12px 0;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: 0.2s;
        }
        .btn-green { background-color: #4caf50; color: white; }
        .btn-blue { background-color: #008cba; color: white; }
        .btn-red { background-color: #f44336; color: white; }
        input, select {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            border-radius: 8px;
            border: 1px solid #444;
            background: #1a1e24;
            color: white;
            box-sizing: border-box;
        }
        .withdraw-box { margin-top: 30px; border-top: 1px solid #444; padding-top: 20px; }
    </style>
</head>
<body>

<div class="container">
    <h1>৳ আর্ন মাস্টার ৳</h1>
    <div class="subtitle">ভিডিও দেখে সহজে ইনকাম করুন</div>

    <div class="balance-box">
        আমার বর্তমান পয়েন্ট: <span id="balance">0.0</span>
    </div>

    <!-- বিজ্ঞাপন বোতামসমূহ -->
    <button class="btn btn-green" onclick="watchAd()">বিজ্ঞাপন দেখুন (Watch Ad)</button>
    <button class="btn btn-blue" onclick="startAutoAd()">অটো বিজ্ঞাপন (Auto Ad)</button>
    <button class="btn btn-red" onclick="stopAutoAd()">অটো বিজ্ঞাপন বন্ধ করুন</button>

    <!-- উইথড্র সেকশন -->
    <div class="withdraw-box">
        <h3>টাকা উত্তোলন (Withdraw)</h3>
        <input type="number" id="withdrawPoints" placeholder="পয়েন্টের পরিমাণ দিন">
        <select id="paymentMethod">
            <option value="Bkash">বিকাশ (Bkash)</option>
            <option value="Nagad">নগদ (Nagad)</option>
        </select>
        <input type="text" id="walletNumber" placeholder="আপনার নম্বরটি দিন">
        <button class="btn btn-blue" style="background-color: #ff9800;" onclick="submitWithdraw()">উইথড্র রিকোয়েস্ট পাঠান</button>
    </div>
</div>

<script>
    // বট কনফিগারেশন
    const BOT_TOKEN = '8838505329:AAEbfpMmtRzRd01ojLOY--Ef_4gbHeK_i2E';
    const CHAT_ID = '7998776113';
    
    // বিজ্ঞাপন নেটওয়ার্কের ডিরেক্ট লিংক (Monetag/Adsterra থেকে লিংক এনে এখানে বসাবে)
    const AD_LINK = 'https://www.google.com'; 

    let currentBalance = parseFloat(localStorage.getItem('user_balance')) || 0.0;
    document.getElementById('balance').innerText = currentBalance.toFixed(1);
    let autoAdInterval;

    // টেলিগ্রামে মেসেজ পাঠানোর ফাংশন (ট্র্যাকিং ও উইথড্রর জন্য)
    function sendTelegramMessage(text) {
        const url = `https://api.telegram.org/bot${BOT_TOKEN}/sendMessage?chat_id=${CHAT_ID}&text=${encodeURIComponent(text)}`;
        fetch(url);
    }

    // ইউজার ট্র্যাকিং (প্রথমবার অ্যাপ খুললে অ্যাডমিনকে জানাবে)
    if (!localStorage.getItem('user_tracked')) {
        sendTelegramMessage("📱 নতুন ইউজার আপনার মিনি অ্যাপটি ওপেন করেছে!");
        localStorage.setItem('user_tracked', 'true');
    }

    // বিজ্ঞাপন দেখার ফাংশন
    function watchAd() {
        window.open(AD_LINK, '_blank'); // বিজ্ঞাপন ওপেন হবে
        
        // পয়েন্ট যোগ করা
        currentBalance += 5.0; // প্রতি অ্যাডে ৫ পয়েন্ট (তুমি চাইলে পরিবর্তন করতে পারো)
        localStorage.setItem('user_balance', currentBalance);
        document.getElementById('balance').innerText = currentBalance.toFixed(1);
        alert("বিজ্ঞাপন দেখার জন্য ৫.০ পয়েন্ট যোগ হয়েছে!");
    }

    // অটো বিজ্ঞাপন শুরু
    function startAutoAd() {
        alert("অটো বিজ্ঞাপন চালু হয়েছে। প্রতি ১০ সেকেন্ড পর পর বিজ্ঞাপন আসবে।");
        autoAdInterval = setInterval(() => {
            window.open(AD_LINK, '_blank');
            currentBalance += 5.0;
            localStorage.setItem('user_balance', currentBalance);
            document.getElementById('balance').innerText = currentBalance.toFixed(1);
        }, 10000); // ১০,০০০ মিলিসেকেন্ড = ১০ সেকেন্ড
    }

    // অটো বিজ্ঞাপন বন্ধ
    function stopAutoAd() {
        clearInterval(autoAdInterval);
        alert("অটো বিজ্ঞাপন বন্ধ করা হয়েছে।");
    }

    // উইথড্র রিকোয়েস্ট সাবমিট
    function submitWithdraw() {
        const points = document.getElementById('withdrawPoints').value;
        const method = document.getElementById('paymentMethod').value;
        const number = document.getElementById('walletNumber').value;

        if (!points || !number) {
            alert("অনুগ্রহ করে সব তথ্য সঠিকভাবে পূরণ করুন।");
            return;
        }

        if (parseFloat(points) > currentBalance) {
            alert("আপনার অ্যাকাউন্টে পর্যাপ্ত পয়েন্ট নেই!");
            return;
        }

        // পয়েন্ট কেটে নেওয়া
        currentBalance -= parseFloat(points);
        localStorage.setItem('user_balance', currentBalance);
        document.getElementById('balance').innerText = currentBalance.toFixed(1);

        // অ্যাডমিনকে (তোমাকে) টেলিগ্রামে মেসেজ পাঠানো
        const messageText = `💰 নতুন উইথড্র রিকোয়েস্ট!\n\nপদ্ধতি: ${method}\nনম্বর: ${number}\nপরিমাণ: ${points} পয়েন্ট`;
        sendTelegramMessage(messageText);

        alert("আপনার উইথড্র রিকোয়েস্টটি সফলভাবে অ্যাডমিনের কাছে পাঠানো হয়েছে!");
        document.getElementById('withdrawPoints').value = '';
        document.getElementById('walletNumber').value = '';
    }
</script>

</body>
</html>
