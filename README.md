<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hospital Patients-handover-map</title>
    
    <!-- FontAwesome Icons & Google Fonts Cairo -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800&display=swap" rel="stylesheet">

    <style>
        :root {
            --color-men: #065f46;         /* أخضر غامق */
            --color-women: #7e22ce;       /* بنفسجي */
            --color-med-icu: #dc2626;     /* أحمر */
            --color-surg-icu: #0f2b48;    /* أزرق غامق چويتي (Navy) */
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

        /* شريط التنسيق والتحكم العلوي */
        .controls-card {
            max-width: 1150px;
            margin: 0 auto 15px auto;
            background: #ffffff;
            border-radius: 12px;
            padding: 10px 18px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 12px;
            flex-wrap: wrap;
            border: 1px solid #e2e8f0;
        }

        .tool-group {
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }

        .tool-title {
            font-size: 13px;
            font-weight: 700;
            color: #475569;
        }

        .btn {
            border: none;
            border-radius: 8px;
            padding: 7px 14px;
            font-size: 13px;
            font-weight: 700;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            gap: 6px;
            transition: all 0.2s ease;
        }

        .btn-pdf {
            background: linear-gradient(135deg, #10b981, #059669);
            color: white;
            box-shadow: 0 3px 8px rgba(16, 185, 129, 0.2);
        }

        .btn-reset {
            background: #ef4444;
            color: white;
            box-shadow: 0 3px 8px rgba(239, 68, 68, 0.2);
        }

        .btn-fmt {
            background: #0f172a;
            color: white;
            padding: 6px 12px;
        }

        .btn-add-patient {
            background: #ffffff;
            color: #0f172a;
            padding: 5px 10px;
            font-size: 12px;
            border-radius: 6px;
            font-weight: 800;
        }

        .color-dot {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            border: 2px solid white;
            box-shadow: 0 2px 4px rgba(0,0,0,0.15);
            cursor: pointer;
            transition: transform 0.2s;
            display: inline-block;
        }
        .color-dot:hover { transform: scale(1.15); }

        /* أزرار اختيار الأقسام (Tabs) */
        .dept-tabs {
            max-width: 1150px;
            margin: 0 auto 15px auto;
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .tab-btn {
            border: none;
            border-radius: 8px;
            padding: 8px 14px;
            font-weight: 700;
            font-size: 13px;
            color: #ffffff;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 6px;
            transition: all 0.2s ease;
            opacity: 0.75;
        }

        .tab-btn:hover { opacity: 0.95; }
        .tab-btn.active {
            opacity: 1;
            box-shadow: 0 3px 10px rgba(0,0,0,0.2);
            outline: 2px solid rgba(0,0,0,0.2);
        }

        .tab-all { background-color: #334155; }
        .tab-men { background-color: var(--color-men); }
        .tab-women { background-color: var(--color-women); }
        .tab-med-icu { background-color: var(--color-med-icu); }
        .tab-surg-icu { background-color: var(--color-surg-icu); }

        /* الورقة الرئيسية */
        #pdf-content {
            max-width: 1150px;
            margin: 0 auto;
            background: #ffffff;
            padding: 20px 25px;
            border-radius: 12px;
            box-shadow: 0 8px 25px rgba(0,0,0,0.04);
            border: 1px solid #e2e8f0;
        }

        .bronze-badge-container {
            text-align: center;
            margin-bottom: 12px;
        }

        .bronze-badge {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            background-color: #e6c594;
            color: #000000;
            font-weight: 800;
            font-size: 18px;
            padding: 6px 24px;
            border-radius: 8px;
            border: 1px solid #d4a359;
            letter-spacing: 0.5px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.06);
        }

        .bronze-badge i { font-size: 20px; }

        .header-box-slim {
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            color: white;
            border-radius: 10px;
            padding: 10px 16px;
            margin-bottom: 18px;
        }

        .header-flex {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 15px;
            flex-wrap: wrap;
        }

        .hospital-title-slim {
            font-size: 16px;
            font-weight: 800;
            color: #facc15;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .header-grid-slim {
            display: flex;
            align-items: center;
            gap: 10px;
            justify-content: flex-end;
            flex-wrap: wrap;
        }

        .info-field-slim {
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 13px;
            background: rgba(255, 255, 255, 0.1);
            padding: 4px 8px;
            border-radius: 6px;
        }

        .info-field-slim i {
            color: #38bdf8;
            font-size: 14px;
        }

        .info-field-slim input, .info-field-slim select {
            background: #ffffff;
            border: none;
            padding: 4px 8px;
            border-radius: 4px;
            font-weight: 700;
            color: #0f172a;
            outline: none;
            font-size: 12px;
        }

        /* كروت الأقسام */
        .dept-card {
            margin-bottom: 22px;
            border-radius: 10px;
            overflow: hidden;
            border: 1px solid #e2e8f0;
            background: #fff;
        }

        .dept-banner {
            padding: 8px 15px;
            color: white;
            font-weight: 700;
            font-size: 15px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .dept-title {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .dept-men .dept-banner { background-color: var(--color-men); }
        .dept-women .dept-banner { background-color: var(--color-women); }
        .dept-med-icu .dept-banner { background-color: var(--color-med-icu); }
        .dept-surg-icu .dept-banner { background-color: var(--color-surg-icu); }

        /* شريط معلومات القسم الخاص لكل قسم */
        .dept-sub-header {
            background: #f1f5f9;
            padding: 6px 15px;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
            border-bottom: 1px solid #e2e8f0;
            font-size: 12px;
        }

        .dept-sub-field {
            display: flex;
            align-items: center;
            gap: 5px;
            color: #334155;
            font-weight: 700;
        }

        .dept-sub-field i {
            color: #64748b;
        }

        .dept-sub-field input, .dept-sub-field select {
            border: 1px solid #cbd5e1;
            padding: 3px 6px;
            border-radius: 4px;
            font-size: 12px;
            font-weight: 700;
            color: #0f172a;
            outline: none;
            background: #ffffff;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th {
            background-color: #f8fafc;
            color: #475569;
            font-size: 12px;
            font-weight: 700;
            padding: 8px 10px;
            text-align: right;
            border-bottom: 2px solid #e2e8f0;
        }

        td {
            padding: 8px 10px;
            border-bottom: 1px solid #f1f5f9;
            font-size: 13px;
            color: #334155;
            vertical-align: top;
        }

        .cell-editable {
            outline: none;
            min-height: 22px;
            padding: 3px;
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
            padding: 2px 6px;
            border-radius: 4px;
            font-size: 12px;
        }

        .footer-banner {
            margin-top: 20px;
            padding-top: 12px;
            border-top: 1px dashed #cbd5e1;
            text-align: center;
            font-size: 11px;
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
            body { padding: 0; background: white; -webkit-print-color-adjust: exact; print-color-adjust: exact; }
            #pdf-content { box-shadow: none; padding: 0; border: none; width: 100%; max-width: 100%; }
            @page { size: A4 portrait; margin: 8mm; }
        }
    </style>
</head>
<body>

    <!-- أدوات التنسيق والتحكم -->
    <div class="controls-card">
        <div class="tool-group">
            <button class="btn btn-pdf" onclick="generatePDF()">
                <i class="fa-solid fa-print"></i> طباعة / حفظ كـ (PDF)
            </button>
            <button class="btn btn-reset" onclick="clearData()">
                <i class="fa-solid fa-rotate-right"></i> تصفير البيانات
            </button>
        </div>

        <div class="tool-group">
            <span class="tool-title"><i class="fa-solid fa-pen-to-square"></i> تنسيق النص المحدد:</span>
            
            <button class="btn btn-fmt" onmousedown="applyFormat(event, 'bold')" title="خط عريض Bold">
                <i class="fa-solid fa-bold"></i> غامق
            </button>

            <button class="btn btn-fmt" style="background:#64748b;" onmousedown="applyFormat(event, 'removeFormat')" title="إزالة التنسيق">
                <i class="fa-solid fa-remove-format"></i> مسح التنسيق
            </button>

            <span class="tool-title" style="margin-right:8px;"><i class="fa-solid fa-palette"></i> الألوان:</span>
            <div class="color-dot" style="background:#dc2626;" onmousedown="applyColor(event, '#dc2626')" title="أحمر"></div>
            <div class="color-dot" style="background:#16a34a;" onmousedown="applyColor(event, '#16a34a')" title="أخضر"></div>
            <div class="color-dot" style="background:#2563eb;" onmousedown="applyColor(event, '#2563eb')" title="أزرق"></div>
            <div class="color-dot" style="background:#0f2b48;" onmousedown="applyColor(event, '#0f2b48')" title="أزرق غامق چويتي"></div>
            <div class="color-dot" style="background:#7e22ce;" onmousedown="applyColor(event, '#7e22ce')" title="بنفسجي"></div>
            <div class="color-dot" style="background:#065f46;" onmousedown="applyColor(event, '#065f46')" title="أخضر غامق"></div>
            <div class="color-dot" style="background:#000000;" onmousedown="applyColor(event, '#000000')" title="أسود"></div>
            <input type="color" id="customColorPicker" onchange="changeCustomColor(this.value)" style="width:24px; height:24px; border:none; cursor:pointer; background:none;">
        </div>
    </div>

    <!-- أزرار الأقسام (Tabs) -->
    <div class="dept-tabs action-element">
        <button class="tab-btn tab-all active" onclick="showTab('all', this)">
            <i class="fa-solid fa-border-all"></i> عرض جميع الأقسام
        </button>
        <button class="tab-btn tab-men" onclick="showTab('dept-men', this)">
            <i class="fa-solid fa-mars"></i> ردهة الرجال
        </button>
        <button class="tab-btn tab-women" onclick="showTab('dept-women', this)">
            <i class="fa-solid fa-venus"></i> ردهة النساء
        </button>
        <button class="tab-btn tab-med-icu" onclick="showTab('dept-med-icu', this)">
            <i class="fa-solid fa-heart-pulse"></i> العناية الباطنية
        </button>
        <button class="tab-btn tab-surg-icu" onclick="showTab('dept-surg-icu', this)">
            <i class="fa-solid fa-user-nurse"></i> العناية الجراحية
        </button>
    </div>

    <!-- المحتوى القابل للتعديل والطباعة -->
    <div id="pdf-content">

        <div class="bronze-badge-container">
            <div class="bronze-badge">
                <i class="fa-solid fa-hospital-user"></i>
                <span>Hospital Patients-handover-map</span>
            </div>
        </div>

        <!-- 2. صندوق المستشفى مع اليوم والتاريخ العامين -->
        <div class="header-box-slim">
            <div class="header-flex">
                <div class="hospital-title-slim">
                    <i class="fa-solid fa-hospital"></i>
                    <span>مستشفى طوارىء ام الربيعين 🌼</span>
                </div>

                <div class="header-grid-slim">
                    <div class="info-field-slim">
                        <i class="fa-solid fa-calendar-day"></i>
                        <select id="mainDaySelect" onchange="saveData()">
                            <option>السبت</option>
                            <option>الأحد</option>
                            <option>الإثنين</option>
                            <option>الثلاثاء</option>
                            <option>الأربعاء</option>
                            <option>الخميس</option>
                            <option>الجمعة</option>
                        </select>
                    </div>

                    <div class="info-field-slim">
                        <i class="fa-solid fa-calendar-check"></i>
                        <input type="date" id="mainDateInput" onchange="saveData()">
                    </div>
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
            <div class="dept-sub-header">
                <div class="dept-sub-field" style="flex:1;">
                    <i class="fa-solid fa-user-doctor"></i>
                    <span>تسليم إلى:</span>
                    <input type="text" id="doc-men" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:260px;">
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-day"></i>
                    <select id="day-men" onchange="saveData()">
                        <option>السبت</option><option>الأحد</option><option>الإثنين</option><option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                    </select>
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-check"></i>
                    <input type="date" id="date-men" onchange="saveData()">
                </div>
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
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
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
            <div class="dept-sub-header">
                <div class="dept-sub-field" style="flex:1;">
                    <i class="fa-solid fa-user-doctor"></i>
                    <span>تسليم إلى:</span>
                    <input type="text" id="doc-women" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:260px;">
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-day"></i>
                    <select id="day-women" onchange="saveData()">
                        <option>السبت</option><option>الأحد</option><option>الإثنين</option><option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                    </select>
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-check"></i>
                    <input type="date" id="date-women" onchange="saveData()">
                </div>
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
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
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
            <div class="dept-sub-header">
                <div class="dept-sub-field" style="flex:1;">
                    <i class="fa-solid fa-user-doctor"></i>
                    <span>تسليم إلى:</span>
                    <input type="text" id="doc-med-icu" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:260px;">
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-day"></i>
                    <select id="day-med-icu" onchange="saveData()">
                        <option>السبت</option><option>الأحد</option><option>الإثنين</option><option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                    </select>
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-check"></i>
                    <input type="date" id="date-med-icu" onchange="saveData()">
                </div>
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
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">سالم علي</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">د. خالد العرجان</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">IHD<br>DM, St elevation<br>in lead v1-v3</div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()">عمل ايكو غداً صباحاً</div></td>
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
            <div class="dept-sub-header">
                <div class="dept-sub-field" style="flex:1;">
                    <i class="fa-solid fa-user-doctor"></i>
                    <span>تسليم إلى:</span>
                    <input type="text" id="doc-surg-icu" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:260px;">
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-day"></i>
                    <select id="day-surg-icu" onchange="saveData()">
                        <option>السبت</option><option>الأحد</option><option>الإثنين</option><option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                    </select>
                </div>
                <div class="dept-sub-field">
                    <i class="fa-solid fa-calendar-check"></i>
                    <input type="date" id="date-surg-icu" onchange="saveData()">
                </div>
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
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
                        <td><div class="cell-editable" contenteditable="true" oninput="saveData()"></div></td>
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

        /* --- حفظ آخر تحديد نصي (Selection) --- */
        let savedRange = null;

        function restoreSelection() {
            if (savedRange) {
                const sel = window.getSelection();
                sel.removeAllRanges();
                sel.addRange(savedRange);
            }
        }

        document.addEventListener('selectionchange', () => {
            const sel = window.getSelection();
            if (sel.rangeCount > 0 && !sel.isCollapsed) {
                savedRange = sel.getRangeAt(0);
            }
        });

        function applyColor(event, color) {
            event.preventDefault();
            restoreSelection();
            document.execCommand('foreColor', false, color);
            saveData();
        }

        function changeCustomColor(color) {
            restoreSelection();
            document.execCommand('foreColor', false, color);
            saveData();
        }

        function applyFormat(event, command) {
            event.preventDefault();
            restoreSelection();
            document.execCommand(command, false, null);
            saveData();
        }

        /* --- التنقل بين الأقسام عبر الأزرار (Tabs) --- */
        function showTab(targetClass, btn) {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');

            const cards = document.querySelectorAll('.dept-card');
            cards.forEach(card => {
                if (targetClass === 'all' || card.classList.contains(targetClass)) {
                    card.style.display = 'block';
                } else {
                    card.style.display = 'none';
                }
            });
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
                mainDay: document.getElementById('mainDaySelect').value,
                mainDate: document.getElementById('mainDateInput').value,
                
                // الأطباء المستلمين لكل قسم
                docMen: document.getElementById('doc-men').value,
                dayMen: document.getElementById('day-men').value,
                dateMen: document.getElementById('date-men').value,

                docWomen: document.getElementById('doc-women').value,
                dayWomen: document.getElementById('day-women').value,
                dateWomen: document.getElementById('date-women').value,

                docMedIcu: document.getElementById('doc-med-icu').value,
                dayMedIcu: document.getElementById('day-med-icu').value,
                dateMedIcu: document.getElementById('date-med-icu').value,

                docSurgIcu: document.getElementById('doc-surg-icu').value,
                daySurgIcu: document.getElementById('day-surg-icu').value,
                dateSurgIcu: document.getElementById('date-surg-icu').value,

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
            const todayStr = new Date().toISOString().split('T')[0];

            if (saved) {
                const data = JSON.parse(saved);
                if(data.mainDay) document.getElementById('mainDaySelect').value = data.mainDay;
                if(data.mainDate) document.getElementById('mainDateInput').value = data.mainDate;

                if(data.docMen) document.getElementById('doc-men').value = data.docMen;
                if(data.dayMen) document.getElementById('day-men').value = data.dayMen;
                if(data.dateMen) document.getElementById('date-men').value = data.dateMen;

                if(data.docWomen) document.getElementById('doc-women').value = data.docWomen;
                if(data.dayWomen) document.getElementById('day-women').value = data.dayWomen;
                if(data.dateWomen) document.getElementById('date-women').value = data.dateWomen;

                if(data.docMedIcu) document.getElementById('doc-med-icu').value = data.docMedIcu;
                if(data.dayMedIcu) document.getElementById('day-med-icu').value = data.dayMedIcu;
                if(data.dateMedIcu) document.getElementById('date-med-icu').value = data.dateMedIcu;

                if(data.docSurgIcu) document.getElementById('doc-surg-icu').value = data.docSurgIcu;
                if(data.daySurgIcu) document.getElementById('day-surg-icu').value = data.daySurgIcu;
                if(data.dateSurgIcu) document.getElementById('date-surg-icu').value = data.dateSurgIcu;

                if(data.tables) {
                    if(data.tables.men) document.getElementById('men-tbody').innerHTML = data.tables.men;
                    if(data.tables.women) document.getElementById('women-tbody').innerHTML = data.tables.women;
                    if(data.tables.medIcu) document.getElementById('med-icu-tbody').innerHTML = data.tables.medIcu;
                    if(data.tables.surgIcu) document.getElementById('surg-icu-tbody').innerHTML = data.tables.surgIcu;
                }
            } else {
                document.getElementById('mainDateInput').value = todayStr;
                document.getElementById('date-men').value = todayStr;
                document.getElementById('date-women').value = todayStr;
                document.getElementById('date-med-icu').value = todayStr;
                document.getElementById('date-surg-icu').value = todayStr;
            }
        }

        function clearData() {
            if (confirm('هل أنت تأكد من مسح جميع البيانات المدخلة والبدء من جديد؟')) {
                localStorage.removeItem('handoverDataMap');
                location.reload();
            }
        }

        function generatePDF() {
            window.print();
        }

        window.onload = loadData;
    </script>
</body>
</html>
