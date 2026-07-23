<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hospital Patients-handover-map</title>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:wght@400;700&family=Aref+Ruqaa:wght@400;700&family=Beiruti:wght@400;600;700;800&family=Cairo:wght@400;600;700;800&family=Tajawal:wght@400;700&display=swap" rel="stylesheet">

    <style>
        :root {
            --color-men: #065f46;         /* أخضر غامق */
            --color-women: #7e22ce;       /* بنفسجي */
            --color-med-icu: #dc2626;     /* أحمر */
            --color-surg-icu: #2563eb;    /* أزرق */
            --bg-body: #f8fafc;
            --main-font: 'Cairo', sans-serif;
        }

        * {
            box-sizing: border-box;
            font-family: var(--main-font);
        }

        body {
            background-color: var(--bg-body);
            margin: 0;
            padding: 15px;
            color: #1e293b;
        }

        /* النافذة المنبثقة لإدخال كلمة المرور */
        #authModal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(4px);
            z-index: 99999;
            display: none;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .auth-card {
            background: #ffffff;
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            text-align: center;
            max-width: 360px;
            width: 100%;
        }

        .auth-card i {
            font-size: 40px;
            color: #2563eb;
            margin-bottom: 10px;
        }

        .auth-card h3 {
            margin: 0 0 8px 0;
            color: #0f172a;
            font-size: 17px;
        }

        .auth-card p {
            color: #64748b;
            font-size: 13px;
            margin-bottom: 15px;
        }

        .auth-card input {
            width: 100%;
            padding: 10px;
            border: 1.5px solid #cbd5e1;
            border-radius: 8px;
            font-size: 15px;
            text-align: center;
            outline: none;
            margin-bottom: 12px;
