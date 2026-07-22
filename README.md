<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Patients Handover Map</title>
    
    <!-- استدعاء الخطوط بما فيها خط بيروتي -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:wght@400;700&family=Aref+Ruqaa:wght@400;700&family=Beiruti:wght@400;600;700&family=Cairo:wght@400;600;700&family=Tajawal:wght@400;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --main-font: 'Cairo', sans-serif;
        }

        body {
            font-family: var(--main-font);
            direction: rtl;
        }

        /* اختيار الخطوط */
        .font-control {
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .font-control select {
            padding: 5px 10px;
            border-radius: 5px;
            border: 1px solid #ccc;
            font-family: inherit;
        }

        /* زر إضافة مكان/قسم جديد */
        .add-place-btn {
            background-color: #28a745;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 5px;
            cursor: pointer;
            font-family: inherit;
            font-weight: bold;
            margin-bottom: 15px;
        }

        .add-place-btn:hover {
            background-color: #218838;
        }

        /* عنوان القسم القابل للتعديل */
        .place-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-top: 20px;
            margin-bottom: 10px;
        }

        .place-title-input {
            font-family: inherit;
            font-size: 1.2rem;
            font-weight: bold;
            border: 1px dashed #ccc;
            padding: 4px 8px;
            border-radius: 4px;
            background: transparent;
        }

        .place-title-input:focus {
            border-color: #007bff;
            background: #fff;
            outline: none;
        }
    </style>
</head>
<body>

    <!-- قائمة اختيار الخط -->
    <div class="font-control">
        <label for="fontSelect"><strong>نوع الخط:</strong></label>
        <select id="fontSelect" onchange="changeAppFont(this.value)">
            <option value="'Cairo', sans-serif">Cairo (الافتراضي)</option>
            <option value="'Beiruti', sans-serif">Beiruti (بيروتي)</option>
            <option value="'Tajawal', sans-serif">Tajawal (تجوال)</option>
            <option value="'Amiri', serif">Amiri (أميري)</option>
            <option value="'Aref Ruqaa', serif">Aref Ruqaa (رقعة)</option>
        </select>
    </div>

    <!-- زر إضافة مكان خامس وسادس... إلخ -->
    <button class="add-place-btn" onclick="addNewPlace()">+ إضافة مكان/قسم جديد</button>

    <!-- الحاوية الرئيسية للأماكن -->
    <div id="placesContainer">
        <!-- يتم إضافة الأماكن هنا تلقائياً -->
    </div>

    <script>
        let placeCounter = 0;

        // تغيير الخط لكل الصفحة
        function changeAppFont(selectedFont) {
            document.body.style.fontFamily = selectedFont;
            document.documentElement.style.setProperty('--main-font', selectedFont);
        }

        // إضافة مكان/قسم جديد (خامس، سادس... بدون حد)
        function addNewPlace(customName) {
            placeCounter++;
            const container = document.getElementById('placesContainer');
            
            const placeId = 'place-' + placeCounter;
            const defaultName = customName || 'المكان ' + placeCounter;

            const placeHTML = `
                <div class="place-block" id="${placeId}" style="margin-bottom: 25px; border-bottom: 2px solid #eee; padding-bottom: 15px;">
                    <div class="place-header">
                        <input type="text" class="place-title-input" value="${defaultName}" title="اضغط لتعديل الاسم" />
                        <button onclick="removePlace('${placeId}')" style="background:#dc3545; color:white; border:none; border-radius:3px; padding:3px 8px; cursor:pointer;">حذف المكان</button>
                    </div>
                    <!-- هنا أضف بقية تفاصيل كروت المرضى المعتادة لديك -->
                </div>
            `;

            container.insertAdjacentHTML('beforeend', placeHTML);
        }

        function removePlace(id) {
            if(confirm('هل تريد حذف هذا المكان؟')) {
                document.getElementById(id).remove();
            }
        }

        // إضافة أماكن افتراضية عند التحميل
        window.onload = function() {
            addNewPlace('المكان الأول');
            addNewPlace('المكان الثاني');
            addNewPlace('المكان الثالث');
            addNewPlace('المكان الرابع');
        };
    </script>
</body>
</html>
