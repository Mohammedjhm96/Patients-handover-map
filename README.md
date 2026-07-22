<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hospital Patients-handover-map</title>
    
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800&display=swap" rel="stylesheet">

    <style>
        :root {
            --color-men: #065f46;         /* أخضر غامق */
            --color-women: #7e22ce;       /* بنفسجي */
            --color-med-icu: #dc2626;     /* أحمر */
            --color-surg-icu: #2563eb;    /* أزرق ملفت للعناية الجراحية */
            --bg-body: #f8fafc;
        }

        * {
            box-sizing: border-box;
            font-family: 'Cairo', sans-serif;
        }

        body {
            background-color: var(--bg-body);
            margin: 0;
            padding: 15px;
            color: #1e293b;
        }

        /* شريط التنسيق والتحكم العلوي (ثابت مع التمرير Sticky) */
        .controls-card {
            max-width: 1150px;
            margin: 0 auto 15px auto;
            background: #ffffff;
            border-radius: 12px;
            padding: 10px 18px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            display: flex;
            align-items: center;
            justify-content: space-between
