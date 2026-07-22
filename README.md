<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Patients Handover Map</title>
    
    <!-- خطوط Google Fonts بما فيها خط بيروتي -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:wght@400;700&family=Aref+Ruqaa:wght@400;700&family=Beiruti:wght@400;600;700&family=Cairo:wght@400;600;700&family=Tajawal:wght@400;700&family=Vazirmatn:wght@400;600;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --main-font: 'Cairo', sans-serif;
        }

        body {
            font-family: var(--main-font);
            background-color: #f4f6f9;
            margin: 0;
            padding: 20px;
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        header {
            background: #ffffff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
            margin-bottom: 25px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
        }

        h1 {
            margin: 0;
            font-size: 1.8rem;
            color: #1e293b;
        }

        .controls {
            display: flex;
            gap: 15px;
            align-items: center;
            flex-wrap: wrap;
        }

        .font-selector {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        select, button {
            padding: 10px 16px;
            border-radius: 8px;
            border: 1px solid #cbd5e1;
            font-family: inherit;
            font-size: 0.95rem;
            outline: none;
        }

        button {
            background-color: #2563eb;
            color: white;
            border: none;
            cursor: pointer;
            transition: background 0.2s ease;
            font-weight: 600;
        }

        button:hover {
            background-color: #1d4ed8;
        }

        .btn-add-section {
            background-color: #059669;
        }
        .btn-add-section:hover {
            background-color: #047857;
        }

        /* الأقسام والأرباع */
        .sections-container {
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        .ward-section {
            background: #ffffff;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.03);
            border-top: 5px solid #2563eb;
        }

        .ward-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #f1f5f9;
        }

        .ward-title-input {
            font-size: 1.4rem;
            font-weight: 700;
            border: 1px transparent;
            background: transparent;
            padding: 5px 10px;
            border-radius: 6px;
            color: #0f172a;
            width: 60%;
        }

        .ward-title-input:hover, .ward-title-input:focus {
            border-color: #cbd5e1;
            background: #f8fafc;
        }

        .patient-list {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }

        .patient-card {
            background: #f8fafc;
            border: 1px solid #e2e8f0;
            border-radius: 8px;
            padding: 12px;
            position: relative;
        }

        .patient-card textarea {
            width: 100%;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            padding: 8px;
            box-sizing: border-box;
            resize: vertical;
            font-family: inherit;
            margin-top: 8px;
        }

        .btn-delete {
            background-color: #ef4444;
            padding: 6px 12px;
            font-size: 0.85rem;
        }
        .btn-delete:hover {
            background-color: #dc2626;
        }

        @media print {
            .controls, button, .btn-delete {
                display: none !important;
            }
            body {
                background: white;
                padding: 0;
            }
            .ward-section {
                box-shadow: none;
                border: 1px solid #ccc;
                page-break-inside: avoid;
            }
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>خارطة تسليم المرضى (Handover Map)</h1>
        
        <div class="controls">
            <!-- قائمة التحكم بالخطوط -->
            <div class="font-selector">
                <label for="fontSelect">الخط:</label>
                <select id="fontSelect" onchange="changeFont(this.value)">
                    <option value="'Cairo', sans-serif">Cairo (الافتراضي)</option>
                    <option value="'Beiruti', sans-serif">Beiruti (بيروتي)</option>
                    <option value="'Tajawal', sans-serif">Tajawal (تجوال)</option>
                    <option value="'Amiri', serif">Amiri (أميري)</option>
                    <option value="'Aref Ruqaa', serif">Aref Ruqaa (رقعة)</option>
                    <option value="'Vazirmatn', sans-serif">Vazirmatn (وزير)</option>
                </select>
            </div>

            <button class="btn-add-section" onclick="addSection()">+ إضافة قسم / مكان جديد</button>
            <button onclick="window.print()">طباعة / تصدير PDF</button>
        </div>
    </header>

    <div id="sectionsContainer" class="sections-container">
        <!-- الأقسام ستضاف هنا ديناميكياً -->
    </div>
</div>

<script>
    let sectionCount = 0;

    // تغيير الخط للواجهة بالكامل
    function changeFont(fontName) {
        document.documentElement.style.setProperty('--main-font', fontName);
    }

    // إضافة قسم جديد
    function addSection(title = '') {
        sectionCount++;
        const container = document.getElementById('sectionsContainer');
        
        const sectionId = `section-${sectionCount}`;
        const defaultTitle = title || `القسم / الردهة ${sectionCount}`;

        const sectionHTML = `
            <div class="ward-section" id="${sectionId}">
                <div class="ward-header">
                    <input type="text" class="ward-title-input" value="${defaultTitle}" placeholder="اكتب اسم المكان أو الردهة..." />
                    <div>
                        <button onclick="addPatient('${sectionId}')">+ إضافة مريض</button>
                        <button class="btn-delete" onclick="removeSection('${sectionId}')">حذف القسم</button>
                    </div>
                </div>
                <div class="patient-list" id="patients-${sectionId}">
                    <!-- كروت المرضى هنا -->
                </div>
            </div>
        `;
        
        container.insertAdjacentHTML('beforeend', sectionHTML);
    }

    // حذف قسم بالكامل
    function removeSection(sectionId) {
        if (confirm('هل أنت تأكد من حذف هذا القسم بجميع مرداه؟')) {
            document.getElementById(sectionId).remove();
        }
    }

    // إضافة مريض داخل قسم معين
    function addPatient(sectionId) {
        const patientContainer = document.getElementById(`patients-${sectionId}`);
        const patientId = 'patient-' + Date.now();

        const patientHTML = `
            <div class="patient-card" id="${patientId}">
                <div style="display: flex; justify-content: space-between; align-items: center;">
                    <input type="text" placeholder="اسم المريض / رقم السرير" style="font-weight: bold; width: 70%;" />
                    <button class="btn-delete" style="padding: 2px 6px;" onclick="document.getElementById('${patientId}').remove()">✕</button>
                </div>
                <textarea rows="3" placeholder="تفاصيل الحالة / التشخيص / الخطة..."></textarea>
            </div>
        `;

        patientContainer.insertAdjacentHTML('beforeend', patientHTML);
    }

    // إنشاء قسم رئيسي افتراضي عند فتح الصفحة لأول مرة
    window.onload = function() {
        addSection('الردهة الأولى');
    };
</script>

</body>
</html>
