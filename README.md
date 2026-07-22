<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>خريطة تسليم المرضى - Patient Handover Map</title>
    
    <!-- FontAwesome Icons & Google Fonts Cairo -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800&display=swap" rel="stylesheet">
    <!-- html2pdf Library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>

    <style>
        :root {
            --color-men: #800020;         /* عنابي / حنائي */
            --color-women: #7e22ce;       /* بنفسجي */
            --color-med-icu: #dc2626;     /* أحمر */
            --color-surg-icu: #0f172a;    /* أزرق غامق جداً */
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

        /* شريط تحكم حديث */
        .controls-card {
            max-width: 1150px;
            margin: 0 auto 20px auto;
            background: #ffffff;
            border-radius: 16px;
            padding: 15px 20px;
            box-shadow: 0 4px 20px -2px rgba(0,0,0,0.06);
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 15px;
            flex-wrap: wrap;
            border: 1px solid #e2e8f0;
        }

        .tool-group {
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }

        .tool-title {
            font-size: 13px;
            font-weight: 700;
            color: #64748b;
        }

        .btn {
            border: none;
            border-radius: 10px;
            padding: 8px 16px;
            font-size: 14px;
            font-weight: 700;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s ease;
        }

        .btn-pdf {
            background: linear-gradient(135deg, #10b981, #059669);
            color: white;
            box-shadow: 0 4px 12px rgba(16, 185, 129, 0.25);
        }

        .btn-reset {
            background: #ef4444;
            color: white;
            box-shadow: 0 4px 12px rgba(239, 68, 68, 0.25);
        }

        .btn-add-patient {
            background: #2563eb;
            color: white;
            padding: 6px 12px;
            font-size: 12px;
            border-radius: 8px;
        }

        .color-dot {
            width: 26px;
            height: 26px;
            border-radius: 50%;
            border: 2px solid white;
            box-shadow: 0 2px 5px rgba(0,0,0,0.15);
            cursor: pointer;
            transition: transform 0.2s;
        }
        .color-dot:hover { transform: scale(1.15); }

        /* شريط الأقسام (Tabs) */
        .dept-tabs {
            max-width: 1150px;
            margin: 0 auto 20px auto;
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .tab-btn {
            background: #ffffff;
            border: 1px solid #cbd5e1;
            border-radius: 10px;
            padding: 10px 18px;
            font-weight: 700;
            font-size: 14px;
            color: #475569;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.2s ease;
            box-shadow: 0 2px 5px rgba(0,0,0,0.03);
        }

        .tab-btn:hover {
            border-color: #94a3b8;
            background: #f1f5f9;
        }

        .tab-btn.active {
            background: #0f172a;
            color: #ffffff;
            border-color: #0f172a;
            box-shadow: 0 4px 12px rgba(15, 23, 42, 0.2);
        }

        /* الورقة الرئيسية */
        #pdf-content {
            max-width: 1150px;
            margin: 0 auto;
            background: #ffffff;
            padding: 25px 30px;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.05);
            border: 1px solid #e2e8f0;
        }

        .header-box {
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            color: white;
            border-radius: 14px;
            padding: 20px 25px;
            margin-bottom: 25px;
        }

        .hospital-title {
            text-align: center;
            font-size: 18px;
            font-weight: 700;
            color: #facc15;
            margin-bottom: 6px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }

        .header-title {
            text-align: center;
            font-size: 22px;
            font-weight: 800;
            margin-bottom: 18px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
        }

        .header-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 15px;
            background: rgba(255, 255, 255, 0.08);
            padding: 15px 20px;
            border-radius: 10px;
        }

        .info-field {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 14px;
        }

        .info-field i {
            font-size: 16px;
            color: #38bdf8;
        }

        .info-field input, .info-field select {
            background: rgba(255, 255, 255, 0.95);
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            font-weight: 600;
            color: #0f172a;
            outline: none;
            width: 100%;
        }

        .dept-card {
            margin-bottom: 25px;
            border-radius: 12px;
            overflow: hidden;
            border: 1px solid #e2e8f0;
            background: #fff;
        }

        .dept-banner {
            padding: 10px 18px;
            color: white;
            font-weight: 700;
            font-size: 16px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .dept-title {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .dept-men .dept-banner { background-color: var(--color-men); }
        .dept-women .dept-banner { background-color: var(--color-women); }
        .dept-med-icu .dept-banner { background-color: var(--color-med-icu); }
        .dept-surg-icu .dept-banner { background-color: var(--color-surg-icu); }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th {
            background-color: #f8fafc;
            color: #475569;
            font-size: 13px;
            font-weight: 700;
            padding: 10px 12px;
            text-align: right;
            border-bottom: 2px solid #e2e8f0;
        }

        td {
            padding: 10px 12px;
            border-bottom: 1px solid #f1f5f9;
            font-size: 13px;
            color: #334155;
            vertical-align: top;
        }

        .cell-editable {
            outline: none;
            min-height: 24px;
            padding: 4px;
            border-radius: 4px;
        }

        .cell-editable:focus {
            background-color: #fef9c3;
            box-shadow: inset 0 0 0 1px #fde047;
        }

        .bed-badge {
            display: inline-block;
            background: #e2e8f0;
            color: #1e293b;
            font-weight: 700;
            padding: 2px 8px;
            border-radius: 6px;
            font-size: 12px;
        }

        .footer-banner {
            margin-top: 30px;
            padding-top: 15px;
            border-top: 2px dashed #cbd5e1;
            text-align: center;
            font-size: 12px;
            color: #64748b;
        }

        .footer-banner a {
            color: #2563eb;
            font-weight: 700;
            text-decoration: none;
            word-break: break-all;
        }

        .delete-btn {
            color: #ef4444;
            cursor: pointer;
            opacity: 0.6;
        }

        @media print {
            .controls-card, .dept-tabs, .action-element { display: none !important; }
            .dept-card { display: block !important; }
            body { padding: 0; background: white; }
            #pdf-content { box-shadow: none; padding: 0; border: none; }
        }
    </style>
</head>
<body>

    <!-- أدوات التحكم والتلوين -->
    <div class="controls-card">
        <div class="tool-group">
            <button class="btn btn-pdf" onclick="generatePDF()">
                <i class="fa-solid fa-file-pdf"></i> تصدير الخريطة (PDF)
            </button>
            <button class="btn btn-reset" onclick="clearData()">
                <i class="fa-solid fa-rotate-right"></i> مسح وتصفير البيانات
            </button>
        </div>

        <div class="tool-group">
            <span class="tool-title"><i class="fa-solid fa-palette"></i> تلوين النص المحدد:</span>
            <div class="color-dot" style="background:#dc2626;" onclick="changeTextColor('#dc2626')" title="أحمر"></div>
            <div class="color-dot" style="background:#16a34a;" onclick="changeTextColor('#16a34a')" title="أخضر"></div>
            <div class="color-dot" style="background:#2563eb;" onclick="changeTextColor('#2563eb')" title="أزرق"></div>
            <div class="color-dot" style="background:#7e22ce;" onclick="changeTextColor('#7e22ce')" title="بنفسجي"></div>
            <div class="color-dot" style="background:#800020;" onclick="changeTextColor('#800020')" title="حنائي"></div>
            <div class="color-dot" style="background:#0f172a;" onclick="changeTextColor('#0f172a')" title="أسود"></div>
            <input type="color" id="customColorPicker" onchange="changeTextColor(this.value)" style="width:26px; height:26px; border:none; cursor:pointer; background:none;">
        </div>
    </div>

    <!-- أزرار اختيار الأقسام (Tabs) -->
    <div class="dept-tabs action-element">
        <button class="tab-btn active" onclick="showTab('all', this)">
            <i class="fa-solid fa-border-all"></i> عرض جميع الأقسام
        </button>
        <button class="tab-btn" onclick="showTab('dept-men', this)">
            <i class="fa-solid fa-mars"></i> ردهة الرجال
        </button>
        <button class="tab-btn" onclick="showTab('dept-women', this)">
            <i class="fa-solid fa-venus"></i> ردهة النساء
        </button>
        <button class="tab-btn" onclick="showTab('dept-med-icu', this)">
            <i class="fa-solid fa-heart-pulse"></i> العناية الباطنية
        </button>
        <button class="tab-btn" onclick="showTab('dept-surg-icu', this)">
            <i class="fa-solid fa-user-nurse"></i> العناية الجراحية
        </button>
    </div>

    <!-- المحتوى القابل للتعديل والطباعة -->
    <div id="pdf-content">

        <div class="header-box">
            <div class="hospital-title">
                <span>مستشفى طوارىء ام الربيعين 🌼</span>
            </div>
            <div class="header-title">
                <i class="fa-solid fa-notes-medical"></i>
                <span>خريطة تسليم وتسلّم المرضى (Patient Handover Map)</span>
            </div>

            <div class="header-grid">
                <div class="info-field">
                    <i class="fa-solid fa-calendar-day"></i>
                    <select id="daySelect" onchange="saveData()">
                        <option>السبت</option>
                        <option>الأحد</option>
                        <option>الإثنين</option>
                        <option>الثلاثاء</option>
                        <option>الأربعاء</option>
                        <option>الخميس</option>
                        <option>الجمعة</option>
                    </select>
                </div>

                <div class="info-field">
                    <i class="fa-solid fa-calendar-check"></i>
                    <input type="date" id="dateInput" onchange="saveData()">
                </div>

                <div class="info-field">
                    <i class="fa-solid fa-user-doctor"></i>
                    <input type="text" id="doctorInput" placeholder="تسليم العناية إلى: د. اسم الطبيب المستلم" oninput="saveData()">
                </div>
            </div>
        </div>

        <!-- 1. ردهة الرجال -->
        <div class="dept-card dept-men">
            <div class="dept-banner">
                <div class="dept-title">
                    <i class="fa-solid fa-mars"></i>
                    <span>ردهة الرجال (Male Ward)</span>
                </div>
                <button class="btn btn-add-patient action-element" onclick="addRow('men-tbody')">+ إضافة حالة</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th style="width: 10%;">الحالة</th>
                        <th style="width: 20%;">اسم المريض والعمر</th>
                        <th style="width: 18%;">الطبيب الاختصاص</th>
                        <th style="width: 22%;">التشخيص الطبي</th>
                        <th style="width: 25%;">الخطة العلاجية والتفاصيل</th>
                        <th class="action-element" style="width: 5%;"></th>
                    </tr>
                </thead>
                <tbody id="men-tbody">
                    <tr>
                        <td><span class="bed-badge cell-editable" contenteditable="true" oninput="saveData()">Case 1</span></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">اسم المريض...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">د. اسم الاختصاصي</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">التشخيص...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">الملاحظات والخطة العلاجية...</div></td>
                        <td class="action-element" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- 2. ردهة النساء -->
        <div class="dept-card dept-women">
            <div class="dept-banner">
                <div class="dept-title">
                    <i class="fa-solid fa-venus"></i>
                    <span>ردهة النساء (Female Ward)</span>
                </div>
                <button class="btn btn-add-patient action-element" onclick="addRow('women-tbody')">+ إضافة حالة</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th style="width: 10%;">الحالة</th>
                        <th style="width: 20%;">اسم المريضة والعمر</th>
                        <th style="width: 18%;">الطبيب الاختصاص</th>
                        <th style="width: 22%;">التشخيص الطبي</th>
                        <th style="width: 25%;">الخطة العلاجية والتفاصيل</th>
                        <th class="action-element" style="width: 5%;"></th>
                    </tr>
                </thead>
                <tbody id="women-tbody">
                    <tr>
                        <td><span class="bed-badge cell-editable" contenteditable="true" oninput="saveData()">Case 1</span></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">اسم المريضة...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">د. اسم الاختصاصي</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">التشخيص...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">الملاحظات والخطة العلاجية...</div></td>
                        <td class="action-element" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- 3. العناية الباطنية -->
        <div class="dept-card dept-med-icu">
            <div class="dept-banner">
                <div class="dept-title">
                    <i class="fa-solid fa-heart-pulse"></i>
                    <span>العناية الباطنية (Medical ICU)</span>
                </div>
                <button class="btn btn-add-patient action-element" onclick="addRow('med-icu-tbody')">+ إضافة حالة</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th style="width: 10%;">الحالة</th>
                        <th style="width: 20%;">اسم المريض والعمر</th>
                        <th style="width: 18%;">الطبيب الاختصاص</th>
                        <th style="width: 22%;">التشخيص الطبي</th>
                        <th style="width: 25%;">الخطة العلاجية والتفاصيل</th>
                        <th class="action-element" style="width: 5%;"></th>
                    </tr>
                </thead>
                <tbody id="med-icu-tbody">
                    <tr>
                        <td><span class="bed-badge cell-editable" contenteditable="true" oninput="saveData()">Case 1</span></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">اسم المريض...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">د. اسم الاختصاصي</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">التشخيص...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">الملاحظات والخطة العلاجية...</div></td>
                        <td class="action-element" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- 4. العناية الجراحية -->
        <div class="dept-card dept-surg-icu">
            <div class="dept-banner">
                <div class="dept-title">
                    <i class="fa-solid fa-user-nurse"></i>
                    <span>العناية الجراحية (Surgical ICU)</span>
                </div>
                <button class="btn btn-add-patient action-element" onclick="addRow('surg-icu-tbody')">+ إضافة حالة</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th style="width: 10%;">الحالة</th>
                        <th style="width: 20%;">اسم المريض والعمر</th>
                        <th style="width: 18%;">الطبيب الاختصاص</th>
                        <th style="width: 22%;">التشخيص الطبي</th>
                        <th style="width: 25%;">الخطة العلاجية والتفاصيل</th>
                        <th class="action-element" style="width: 5%;"></th>
                    </tr>
                </thead>
                <tbody id="surg-icu-tbody">
                    <tr>
                        <td><span class="bed-badge cell-editable" contenteditable="true" oninput="saveData()">Case 1</span></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">اسم المريض...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">د. اسم الاختصاصي</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">التشخيص...</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">الملاحظات والخطة العلاجية...</div></td>
                        <td class="action-element" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="footer-banner">
            <div>لتعديل أو تحديث بيانات الخريطة عبر الموقع الإلكتروني:</div>
            <a href="#" target="_blank" id="appLiveUrl">جاري تحميل الرابط...</a>
        </div>

    </div>

    <script>
        document.getElementById('appLiveUrl').href = window.location.href;
        document.getElementById('appLiveUrl').innerText = window.location.href;

        /* --- التنقل بين الأقسام عبر الأزرار (Tabs) --- */
        function showTab(targetClass, btn) {
            // تحديث تصميم الأزرار
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');

            // إظهار وإخفاء الكروت بناءً على الخيار المحدد
            const cards = document.querySelectorAll('.dept-card');
            cards.forEach(card => {
                if (targetClass === 'all' || card.classList.contains(targetClass)) {
                    card.style.display = 'block';
                } else {
                    card.style.display = 'none';
                }
            });
        }

        function changeTextColor(color) {
            document.execCommand('foreColor', false, color);
            saveData();
        }

        function addRow(tbodyId) {
            const tbody = document.getElementById(tbodyId);
            const count = tbody.children.length + 1;
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td><span class="bed-badge cell-editable" contenteditable="true" oninput="saveData()">Case ${count}</span></td>
                <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                <td class="action-element" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
            `;
            tbody.appendChild(tr);
            saveData();
        }

        function deleteRow(btn) {
            btn.closest('tr').remove();
            saveData();
        }

        /* --- حفظ واسترجاع البيانات عبر LocalStorage --- */
        function saveData() {
            const data = {
                day: document.getElementById('daySelect').value,
                date: document.getElementById('dateInput').value,
                doctor: document.getElementById('doctorInput').value,
                tables: {
                    men: document.getElementById('men-tbody').innerHTML,
                    women: document.getElementById('women-tbody').innerHTML,
                    medIcu: document.getElementById('med-icu-tbody').innerHTML,
                    surgIcu: document.getElementById('surg-icu-tbody').innerHTML
                }
            };
            localStorage.setItem('handoverDataMap', JSON.stringify(data));
        }

        function loadData() {
            const saved = localStorage.getItem('handoverDataMap');
            if (saved) {
                const data = JSON.parse(saved);
                if(data.day) document.getElementById('daySelect').value = data.day;
                if(data.date) document.getElementById('dateInput').value = data.date;
                if(data.doctor) document.getElementById('doctorInput').value = data.doctor;
                
                if(data.tables) {
                    if(data.tables.men) document.getElementById('men-tbody').innerHTML = data.tables.men;
                    if(data.tables.women) document.getElementById('women-tbody').innerHTML = data.tables.women;
                    if(data.tables.medIcu) document.getElementById('med-icu-tbody').innerHTML = data.tables.medIcu;
                    if(data.tables.surgIcu) document.getElementById('surg-icu-tbody').innerHTML = data.tables.surgIcu;
                }
            } else {
                document.getElementById('dateInput').valueAsDate = new Date();
            }
        }

        function clearData() {
            if (confirm('هل أنت تأكد من مسح جميع البيانات المدخلة والبدء من جديد؟')) {
                localStorage.removeItem('handoverDataMap');
                location.reload();
            }
        }

        function generatePDF() {
            // إظهار كافة الأقسام قبل تصدير PDF لتظهر في الملف بالكامل
            const cards = document.querySelectorAll('.dept-card');
            cards.forEach(card => card.style.display = 'block');

            const element = document.getElementById('pdf-content');
            const actions = document.querySelectorAll('.action-element');
            actions.forEach(el => el.style.display = 'none');

            const opt = {
                margin:       [8, 8, 8, 8],
                filename:     `Patient_Handover_Map_${new Date().toISOString().slice(0,10)}.pdf`,
                image:        { type: 'jpeg', quality: 0.98 },
                html2canvas:  { scale: 2, useCORS: true },
                jsPDF:        { unit: 'mm', format: 'a4', orientation: 'portrait' }
            };

            html2pdf().set(opt).from(element).save().then(() => {
                actions.forEach(el => el.style.display = '');
                // إعادة حالة التبويب النشط بعد الانتهاء من التصدير
                const activeTab = document.querySelector('.tab-btn.active');
                const activeOnClick = activeTab ? activeTab.getAttribute('onclick') : '';
                if(activeTab) activeTab.click();
            });
        }

        // تحميل البيانات المحفوظة فور فتح الصفحة
        window.onload = loadData;
    </script>
</body>
</html>
