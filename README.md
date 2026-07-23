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
    
    <!-- html2canvas لتحويل بطاقة المريض إلى صورة -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

    <style>
        :root {
            --color-men: #065f46;
            --color-women: #7e22ce;
            --color-med-icu: #dc2626;
            --color-surg-icu: #2563eb;
            --bg-body: #f8fafc;
            --main-font: 'Cairo', sans-serif;
        }

        * { box-sizing: border-box; font-family: var(--main-font); }
        body { background-color: var(--bg-body); margin: 0; padding: 15px; color: #1e293b; }

        /* Modals (Auth, GCS, Share) */
        #authModal, #gcsModal, #shareModal {
            position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(4px);
            z-index: 99999; display: none; align-items: center; justify-content: center; padding: 20px;
        }

        .auth-card, .gcs-card, .share-card {
            background: #ffffff; padding: 22px; border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3); text-align: center; max-width: 420px; width: 100%;
        }
        .auth-card i, .gcs-card i.head-icon, .share-card i.head-icon { font-size: 36px; color: #2563eb; margin-bottom: 8px; }
        .auth-card h3, .gcs-card h3, .share-card h3 { margin: 0 0 12px 0; color: #0f172a; font-size: 16px; }
        .auth-card p { color: #64748b; font-size: 13px; margin-bottom: 15px; }
        .auth-card input {
            width: 100%; padding: 10px; border: 1.5px solid #cbd5e1; border-radius: 8px;
            font-size: 15px; text-align: center; outline: none; margin-bottom: 12px;
        }
        .btn-group { display: flex; gap: 8px; margin-top: 15px; }
        .btn-group button { flex: 1; padding: 9px; border: none; border-radius: 8px; font-weight: 700; font-size: 13px; cursor: pointer; }
        .btn-confirm { background: #2563eb; color: white; }
        .btn-cancel { background: #e2e8f0; color: #475569; }
        .btn-share-text { background: #16a34a; color: white; }
        .btn-share-img { background: #0284c7; color: white; }
        .error-msg { color: #ef4444; font-size: 12px; font-weight: 700; margin-top: 8px; display: none; }

        /* GCS Modal Form Styles */
        .gcs-calc-field { display: flex; flex-direction: column; gap: 4px; text-align: right; margin-bottom: 10px; }
        .gcs-calc-field label { font-size: 12px; font-weight: 700; color: #334155; }
        .gcs-calc-field select { padding: 7px; border: 1px solid #cbd5e1; border-radius: 6px; font-size: 12px; font-weight: 600; outline: none; background: #f8fafc; }
        .gcs-modal-score { font-size: 15px; font-weight: 800; color: #dc2626; background: #fef2f2; padding: 8px; border-radius: 8px; border: 1px solid #fca5a5; margin-top: 10px; }

        .controls-card {
            max-width: 1150px; margin: 0 auto 15px auto; background: #ffffff; border-radius: 12px;
            padding: 10px 18px; box-shadow: 0 4px 15px rgba(0,0,0,0.08); display: flex;
            align-items: center; justify-content: space-between; gap: 12px; flex-wrap: wrap;
            border: 1px solid #cbd5e1; position: sticky; top: 10px; z-index: 1000;
        }

        .tool-group { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }
        .tool-title { font-size: 13px; font-weight: 700; color: #475569; }

        .btn {
            border: none; border-radius: 8px; padding: 7px 14px; font-size: 13px; font-weight: 700;
            cursor: pointer; display: inline-flex; align-items: center; gap: 6px; transition: all 0.2s ease;
        }
        .btn-mode { background: #0284c7; color: white; }
        .btn-lock { background: #dc2626; color: white; }
        .btn-pdf { background: linear-gradient(135deg, #10b981, #059669); color: white; }
        .btn-reset { background: #ef4444; color: white; }
        .btn-add-dept { background: #4f46e5; color: white; }
        .btn-add-subdept { background: #0d9488; color: white; padding: 4px 8px; font-size: 11px; border-radius: 6px; }
        .btn-fmt { background: #0f172a; color: white; padding: 6px 12px; }

        .font-select-control { padding: 6px 10px; border-radius: 6px; border: 1px solid #cbd5e1; font-size: 12px; font-weight: 700; outline: none; cursor: pointer; background: #fff; }

        .btn-add-patient { background: #ffffff; color: #0f172a; padding: 4px 8px; font-size: 11px; border-radius: 6px; font-weight: 800; }
        .btn-share-patient { background: #2563eb; color: #ffffff; border: none; padding: 2px 6px; font-size: 10px; border-radius: 4px; font-weight: 700; cursor: pointer; margin-right: 4px; display: inline-flex; align-items: center; gap: 3px; }
        .btn-share-patient:hover { background: #1d4ed8; }

        .color-dot { width: 22px; height: 22px; border-radius: 50%; border: 2px solid white; box-shadow: 0 2px 4px rgba(0,0,0,0.15); cursor: pointer; display: inline-block; }

        .dept-tabs { max-width: 1150px; margin: 0 auto 15px auto; display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; }
        .tab-btn {
            border: none; border-radius: 8px; padding: 8px 14px; font-weight: 700; font-size: 13px;
            color: #ffffff; cursor: pointer; display: flex; align-items: center; gap: 6px;
            transition: all 0.2s ease; opacity: 0.75; background-color: #334155;
        }
        .tab-btn:hover { opacity: 0.95; }
        .tab-btn.active { opacity: 1; box-shadow: 0 3px 10px rgba(0,0,0,0.2); outline: 2px solid rgba(0,0,0,0.2); }
        .tab-men { background-color: var(--color-men); }
        .tab-women { background-color: var(--color-women); }
        .tab-med-icu { background-color: var(--color-med-icu); }
        .tab-surg-icu { background-color: var(--color-surg-icu); }

        #pdf-content {
            max-width: 1150px; margin: 0 auto; background: #ffffff; padding: 20px 25px;
            border-radius: 12px; box-shadow: 0 8px 25px rgba(0,0,0,0.04); border: 1px solid #e2e8f0;
        }

        .bronze-badge-container { text-align: center; margin-bottom: 12px; }
        .bronze-badge { display: inline-flex; align-items: center; justify-content: center; gap: 10px; background-color: #e6c594; color: #000000; font-weight: 800; font-size: 18px; padding: 6px 24px; border-radius: 8px; border: 1px solid #d4a359; }

        .header-box-slim { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); color: white; border-radius: 10px; padding: 10px 16px; margin-bottom: 18px; }
        .header-flex { display: flex; align-items: center; justify-content: space-between; gap: 15px; flex-wrap: wrap; }
        .hospital-title-slim { font-size: 16px; font-weight: 800; color: #facc15; display: flex; align-items: center; gap: 6px; }
        .header-grid-slim { display: flex; align-items: center; gap: 10px; justify-content: flex-end; flex-wrap: wrap; }
        .info-field-slim { display: flex; align-items: center; gap: 6px; font-size: 13px; background: rgba(255, 255, 255, 0.1); padding: 4px 8px; border-radius: 6px; }
        .info-field-slim i { color: #38bdf8; font-size: 14px; }
        .info-field-slim input, .info-field-slim select { background: #ffffff; border: none; padding: 4px 8px; border-radius: 4px; font-weight: 700; color: #0f172a; outline: none; font-size: 12px; }

        .dept-card { margin-bottom: 22px; border-radius: 10px; overflow: hidden; border: 1px solid #cbd5e1; background: #fff; }
        .dept-banner { padding: 8px 15px; color: white; font-weight: 700; font-size: 15px; display: flex; align-items: center; justify-content: space-between; }
        .dept-men .dept-banner { background-color: var(--color-men); }
        .dept-women .dept-banner { background-color: var(--color-women); }
        .dept-med-icu .dept-banner { background-color: var(--color-med-icu); }
        .dept-surg-icu .dept-banner { background-color: var(--color-surg-icu); }
        .dept-title { display: flex; align-items: center; gap: 8px; flex: 1; }
        .dept-title-editable { background: transparent; border: 1px dashed transparent; color: white; font-weight: 800; font-size: 15px; padding: 2px 6px; border-radius: 4px; width: 80%; outline: none; }

        .dept-actions { display: flex; align-items: center; gap: 6px; }
        .change-color-btn { background: rgba(255, 255, 255, 0.2); color: white; border: 1px solid rgba(255, 255, 255, 0.4); border-radius: 6px; padding: 4px 8px; font-size: 11px; font-weight: 700; cursor: pointer; position: relative; }
        .change-color-btn input[type="color"] { position: absolute; top: 0; left: 0; width: 100%; height: 100%; opacity: 0; cursor: pointer; }

        .dept-sub-header { background: #f1f5f9; padding: 6px 15px; display: flex; align-items: center; gap: 12px; flex-wrap: wrap; border-bottom: 1px solid #e2e8f0; font-size: 12px; }
        .dept-sub-field { display: flex; align-items: center; gap: 5px; color: #334155; font-weight: 700; }
        .dept-sub-field input, .dept-sub-field select { border: 1px solid #cbd5e1; padding: 3px 6px; border-radius: 4px; font-size: 12px; font-weight: 700; color: #0f172a; outline: none; background: #ffffff; }

        .sub-dept-container { padding: 10px 15px; background-color: #fafafa; }
        .sub-dept-card { margin-top: 10px; margin-bottom: 15px; border: 1px solid #cbd5e1; border-radius: 8px; overflow: hidden; background: #ffffff; }
        .sub-dept-banner { background-color: #475569; color: white; padding: 6px 12px; display: flex; align-items: center; justify-content: space-between; font-size: 13px; }
        .sub-dept-title-editable { background: transparent; border: 1px dashed transparent; color: white; font-weight: 700; font-size: 13px; padding: 1px 4px; border-radius: 4px; width: 70%; outline: none; }

        table { width: 100%; border-collapse: collapse; }
        th { background-color: #f8fafc; color: #475569; font-size: 12px; font-weight: 700; padding: 8px 10px; text-align: right; border-bottom: 2px solid #e2e8f0; }
        td { padding: 8px 10px; border-bottom: 1px solid #f1f5f9; font-size: 13px; color: #334155; vertical-align: middle; }

        .cell-editable { outline: none; min-height: 22px; padding: 3px; border-radius: 4px; }
        .cell-plan { color: #0369a1; font-weight: 600; }
        
        .text-black { color: #000000 !important; font-weight: 600; }
        .text-darkblue { color: #1e3a8a !important; font-weight: 700; }

        .gcs-compact-badge { display: inline-flex; align-items: center; gap: 6px; background: #fff0f0; border: 1px solid #fca5a5; padding: 3px 8px; border-radius: 6px; font-weight: 800; color: #dc2626; font-size: 12px; white-space: nowrap; }
        .gcs-calc-btn { background: #dc2626; color: white; border: none; border-radius: 4px; padding: 2px 5px; font-size: 11px; cursor: pointer; transition: background 0.2s; }
        .gcs-calc-btn:hover { background: #b91c1c; }

        body.read-only-mode .edit-only { display: none !important; }
        body.read-only-mode input, body.read-only-mode select { pointer-events: none; border-color: transparent !important; background: transparent !important; }
        body.read-only-mode .cell-editable { pointer-events: none; }
        body.edit-mode-active .cell-editable:focus { background-color: #fef9c3; box-shadow: inset 0 0 0 1px #fde047; }

        .bed-badge { display: inline-block; background: #e2e8f0; color: #1e293b; font-weight: 700; padding: 2px 6px; border-radius: 4px; font-size: 12px; }

        .footer-banner-box { margin-top: 25px; padding: 12px 18px; background: #f8fafc; border: 1.5px solid #cbd5e1; border-radius: 10px; text-align: center; }
        .footer-banner-box .footer-title { font-size: 12px; font-weight: 700; color: #475569; margin-bottom: 4px; }
        .footer-banner-box a { color: #2563eb; font-weight: 800; font-size: 13px; text-decoration: underline; }
        .developer-credits { margin-top: 10px; font-size: 13px; font-weight: 800; color: #0f172a; direction: ltr; }

        .delete-btn { color: #ef4444; cursor: pointer; }
        .delete-dept-btn { background: rgba(239, 68, 68, 0.85); color: #ffffff; border: none; border-radius: 6px; padding: 4px 8px; cursor: pointer; font-size: 11px; font-weight: 700; }

        .floating-edit-btn {
            position: fixed; bottom: 25px; left: 25px; width: 55px; height: 55px; border-radius: 50%;
            background: #0284c7; color: white; border: none; box-shadow: 0 4px 15px rgba(2, 132, 199, 0.4);
            cursor: pointer; z-index: 9999; display: flex; align-items: center; justify-content: center; font-size: 22px;
            transition: transform 0.6s ease-in-out, background-color 0.3s;
        }

        .floating-edit-btn.active { background: #dc2626; transform: rotate(360deg); box-shadow: 0 4px 15px rgba(220, 38, 38, 0.4); }

        /* منطقة توليد كارت المريض كصورة */
        #singlePatientCardRender {
            position: absolute; left: -9999px; top: -9999px; width: 500px; background: #ffffff; padding: 20px; border-radius: 12px; border: 2px solid #0f172a; font-family: 'Cairo', sans-serif;
        }

        @media print {
            #authModal, #gcsModal, #shareModal, .controls-card, .dept-tabs, .action-element, .edit-only, .floating-edit-btn, .gcs-calc-btn, .btn-share-patient { display: none !important; }
            body { padding: 0; background: white; -webkit-print-color-adjust: exact; print-color-adjust: exact; }
            #pdf-content { box-shadow: none; padding: 0; border: none; width: 100%; max-width: 100%; }
            @page { size: A4 portrait; margin: 8mm; }
        }
    </style>
</head>
<body class="read-only-mode">

    <button class="floating-edit-btn" id="floatingEditBtn" onclick="toggleEditModeFloating()" title="وضع التعديل / قفل">
        <i class="fa-solid fa-lock" id="floatingIcon"></i>
    </button>

    <!-- Auth Modal -->
    <div id="authModal">
        <div class="auth-card">
            <i class="fa-solid fa-lock"></i>
            <h3>تفعيل وضع التعديل</h3>
            <p>يرجى إدخال كلمة المرور الخاصه بالأطباء لتمكين التعديل على البيانات</p>
            <input type="password" id="passInput" placeholder="أدخل كلمة المرور" onkeypress="if(event.key==='Enter') checkPass()">
            <div class="btn-group">
                <button class="btn-confirm" onclick="checkPass()">تأكيد</button>
                <button class="btn-cancel" onclick="closeAuthModal()">إلغاء</button>
            </div>
            <div id="errMsg" class="error-msg">كلمة المرور غير صحيحة!</div>
        </div>
    </div>

    <!-- GCS Modal -->
    <div id="gcsModal">
        <div class="gcs-card">
            <i class="fa-solid fa-brain head-icon"></i>
            <h3>حاسبة Glasgow Coma Scale (GCS)</h3>
            
            <div class="gcs-calc-field">
                <label>Eye Opening (E):</label>
                <select id="modalGcsE" onchange="calcModalGCS()">
                    <option value="4">4 - Spontaneous</option>
                    <option value="3">3 - To sound</option>
                    <option value="2">2 - To pressure</option>
                    <option value="1">1 - None</option>
                </select>
            </div>

            <div class="gcs-calc-field">
                <label>Verbal Response (V):</label>
                <select id="modalGcsV" onchange="calcModalGCS()">
                    <option value="5">5 - Oriented</option>
                    <option value="4">4 - Confused</option>
                    <option value="3">3 - Words</option>
                    <option value="2">2 - Sounds</option>
                    <option value="1">1 - None</option>
                </select>
            </div>

            <div class="gcs-calc-field">
                <label>Motor Response (M):</label>
                <select id="modalGcsM" onchange="calcModalGCS()">
                    <option value="6">6 - Obeys commands</option>
                    <option value="5">5 - Localising</option>
                    <option value="4">4 - Normal flexion</option>
                    <option value="3">3 - Abnormal flexion</option>
                    <option value="2">2 - Extension</option>
                    <option value="1">1 - None</option>
                </select>
            </div>

            <div class="gcs-modal-score">
                Total Score: <span id="modalGcsTotal">15/15</span>
            </div>

            <div class="btn-group">
                <button class="btn-confirm" onclick="applyGCSModal()">حفظ وتحديث</button>
                <button class="btn-cancel" onclick="closeGCSModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <!-- Share Patient Modal -->
    <div id="shareModal">
        <div class="share-card">
            <i class="fa-solid fa-share-nodes head-icon"></i>
            <h3>مشاركة حالة المريض</h3>
            <p id="sharePatientName" style="font-weight:700; color:#0f172a; margin-bottom:10px;"></p>
            
            <div class="btn-group" style="flex-direction:column;">
                <button class="btn-share-text" onclick="sharePatientAsText()"><i class="fa-solid fa-copy"></i> نسخ كأنص مرتب للمشاركة</button>
                <button class="btn-share-img" onclick="downloadPatientAsImage()"><i class="fa-solid fa-image"></i> تحميل / مشاركة كبطاقة صورة</button>
                <button class="btn-cancel" onclick="closeShareModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <!-- Hidden element for image rendering -->
    <div id="singlePatientCardRender"></div>

    <div class="controls-card">
        <div class="tool-group">
            <button class="btn btn-mode" id="toggleEditBtn" onclick="openAuthModal()">
                <i class="fa-solid fa-lock"></i> وضع التعديل (للأطباء)
            </button>
            <button class="btn btn-lock edit-only" onclick="setEditMode(false)">
                <i class="fa-solid fa-lock"></i> إغلاق التعديل
            </button>
            <button class="btn btn-pdf" onclick="generatePDF()">
                <i class="fa-solid fa-print"></i> طباعة / حفظ (PDF)
            </button>
            <button class="btn btn-add-dept edit-only" onclick="addNewDepartment()">
                <i class="fa-solid fa-square-plus"></i> إضافة قسم رئيسي
            </button>
            <button class="btn btn-reset edit-only" onclick="clearData()">
                <i class="fa-solid fa-rotate-right"></i> تصفير البيانات
            </button>
        </div>

        <div class="tool-group edit-only">
            <span class="tool-title"><i class="fa-solid fa-font"></i> الخط وتلوين النص:</span>
            <select class="font-select-control" onchange="changeFontFamily(this.value)">
                <option value="'Cairo', sans-serif">Cairo (الافتراضي)</option>
                <option value="'Beiruti', sans-serif">Beiruti (بيروتي)</option>
                <option value="'Tajawal', sans-serif">Tajawal (تجوال)</option>
                <option value="'Amiri', serif">Amiri (أميري)</option>
                <option value="'Aref Ruqaa', serif">Aref Ruqaa (رقعة)</option>
            </select>

            <button class="btn btn-fmt" onmousedown="applyFormat(event, 'bold')" title="غامق Bold">
                <i class="fa-solid fa-bold"></i>
            </button>
            <button class="btn btn-fmt" style="background:#64748b;" onmousedown="applyFormat(event, 'removeFormat')" title="مسح التنسيق">
                <i class="fa-solid fa-remove-format"></i>
            </button>

            <!-- لوحة اختيار ألوان النص للخطة والتفاصيل -->
            <div class="color-dot" style="background:#0369a1;" title="أزرق" onmousedown="applyColor(event, '#0369a1')"></div>
            <div class="color-dot" style="background:#dc2626;" title="أحمر" onmousedown="applyColor(event, '#dc2626')"></div>
            <div class="color-dot" style="background:#16a34a;" title="أخضر" onmousedown="applyColor(event, '#16a34a')"></div>
            <div class="color-dot" style="background:#7e22ce;" title="بنفسجي" onmousedown="applyColor(event, '#7e22ce')"></div>
            <div class="color-dot" style="background:#000000;" title="أسود" onmousedown="applyColor(event, '#000000')"></div>
        </div>
    </div>

    <div class="dept-tabs action-element" id="tabsContainer">
        <button class="tab-btn tab-all active" onclick="showTab('all', this)">
            <i class="fa-solid fa-border-all"></i> عرض الجميع
        </button>
        <button class="tab-btn tab-men" id="tab-btn-card-men" onclick="showTab('card-men', this)">
            <i class="fa-solid fa-mars"></i> <span id="label-card-men">ردهة الرجال</span>
        </button>
        <button class="tab-btn tab-women" id="tab-btn-card-women" onclick="showTab('card-women', this)">
            <i class="fa-solid fa-venus"></i> <span id="label-card-women">ردهة النساء</span>
        </button>
        <button class="tab-btn tab-med-icu" id="tab-btn-card-med-icu" onclick="showTab('card-med-icu', this)">
            <i class="fa-solid fa-heart-pulse"></i> <span id="label-card-med-icu">العناية الباطنية</span>
        </button>
        <button class="tab-btn tab-surg-icu" id="tab-btn-card-surg-icu" onclick="showTab('card-surg-icu', this)">
            <i class="fa-solid fa-scalpel"></i> <span id="label-card-surg-icu">العناية الجراحية</span>
        </button>
    </div>

    <div id="pdf-content">

        <div class="bronze-badge-container">
            <div class="bronze-badge">
                <i class="fa-solid fa-hospital-user"></i>
                <span>Hospital Patients-handover-map</span>
            </div>
        </div>

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
                            <option>السبت</option><option>الأحد</option><option>الإثنين</option>
                            <option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                        </select>
                    </div>

                    <div class="info-field-slim">
                        <i class="fa-solid fa-calendar-check"></i>
                        <input type="date" id="mainDateInput" onchange="saveData()">
                    </div>
                </div>
            </div>
        </div>

        <div id="departmentsWrapper">
            <!-- 1. ردهة الرجال -->
            <div class="dept-card dept-men" id="card-men">
                <div class="dept-banner" id="banner-card-men">
                    <div class="dept-title">
                        <i class="fa-solid fa-mars"></i>
                        <input type="text" class="dept-title-editable" value="ردهة الرجال (Male Ward)" oninput="updateTabTitle('card-men', this.value); saveData();" />
                    </div>
                    <div class="dept-actions edit-only">
                        <label class="change-color-btn">
                            <i class="fa-solid fa-palette"></i> لون
                            <input type="color" onchange="changeDeptColor('card-men', this.value)">
                        </label>
                        <button class="btn btn-add-subdept" onclick="addSubDepartment('card-men')">+ قسم فرعي</button>
                        <button class="btn btn-add-patient" onclick="addRow('men-tbody', false)">+ حالة</button>
                    </div>
                </div>
                <div class="dept-sub-header">
                    <div class="dept-sub-field" style="flex:1;">
                        <i class="fa-solid fa-user-doctor"></i>
                        <span>تسليم إلى:</span>
                        <input type="text" id="doc-men" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:200px;">
                    </div>
                    <div class="dept-sub-field">
                        <i class="fa-solid fa-phone"></i>
                        <span>الهاتف:</span>
                        <input type="tel" id="phone-men" placeholder="رقم الهاتف" oninput="saveData()" style="width:120px;">
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
                            <th style="width: 18%;">اسم المريض</th>
                            <th style="width: 6%;">العمر</th>
                            <th style="width: 18%;">الطبيب الاختصاص</th>
                            <th style="width: 21%;">التشخيص الطبي</th>
                            <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                            <th class="edit-only" style="width: 5%;"></th>
                        </tr>
                    </thead>
                    <tbody id="men-tbody">
                    </tbody>
                </table>

                <div class="sub-dept-container" id="sub-container-card-men">
                    <div class="sub-dept-card" id="sub-neurosurg">
                        <div class="sub-dept-banner" id="sub-banner-sub-neurosurg" style="background-color: #831843;">
                            <div style="display:flex; align-items:center; gap:6px; flex:1;">
                                <i class="fa-solid fa-brain"></i>
                                <input type="text" class="sub-dept-title-editable" value="ردهة الجراحة العصبية" oninput="saveData()" />
                            </div>
                            <div class="dept-actions edit-only">
                                <label class="change-color-btn">
                                    <i class="fa-solid fa-palette"></i> لون
                                    <input type="color" value="#831843" onchange="changeSubDeptColor('sub-neurosurg', this.value)">
                                </label>
                                <button class="btn btn-add-patient" onclick="addRow('sub-neurosurg-tbody', true)">+ حالة</button>
                                <button class="delete-dept-btn" onclick="removeSubDepartment('sub-neurosurg')"><i class="fa-solid fa-trash"></i> حذف</button>
                            </div>
                        </div>
                        <table>
                            <thead>
                                <tr>
                                    <th style="width: 10%;">الحالة</th>
                                    <th style="width: 18%;">اسم المريض</th>
                                    <th style="width: 6%;">العمر</th>
                                    <th style="width: 14%;">مقياس الوعي (GCS)</th>
                                    <th style="width: 16%;">الطبيب الاختصاص</th>
                                    <th style="width: 16%;">التشخيص الطبي</th>
                                    <th style="width: 15%;">الخطة العلاجية والتفاصيل</th>
                                    <th class="edit-only" style="width: 5%;"></th>
                                </tr>
                            </thead>
                            <tbody id="sub-neurosurg-tbody">
                                <tr>
                                    <td>
                                        <span class="bed-badge cell-editable" contenteditable="false" oninput="saveData()">Case 1</span>
                                        <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                                    </td>
                                    <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">خالد مروان</div></td>
                                    <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">34</div></td>
                                    <td>
                                        <div class="gcs-compact-badge" data-e="4" data-v="5" data-m="6">
                                            <span>GCS: <strong class="gcs-val">15/15</strong></span>
                                            <button class="gcs-calc-btn edit-only" onclick="openGCSModal(this)" title="إعادة حساب GCS">🧮</button>
                                        </div>
                                    </td>
                                    <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">د. ممدوح مصلح</div></td>
                                    <td><div class="cell-editable text-darkblue" contenteditable="false" oninput="saveData()">Head Injury</div></td>
                                    <td><div class="cell-editable cell-plan" contenteditable="false" oninput="saveData()">متابعة حالة الوعي وإجراء الفحوصات</div></td>
                                    <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- 2. ردهة النساء -->
            <div class="dept-card dept-women" id="card-women">
                <div class="dept-banner" id="banner-card-women">
                    <div class="dept-title">
                        <i class="fa-solid fa-venus"></i>
                        <input type="text" class="dept-title-editable" value="ردهة النساء (Female Ward)" oninput="updateTabTitle('card-women', this.value); saveData();" />
                    </div>
                    <div class="dept-actions edit-only">
                        <label class="change-color-btn">
                            <i class="fa-solid fa-palette"></i> لون
                            <input type="color" onchange="changeDeptColor('card-women', this.value)">
                        </label>
                        <button class="btn btn-add-subdept" onclick="addSubDepartment('card-women')">+ قسم فرعي</button>
                        <button class="btn btn-add-patient" onclick="addRow('women-tbody', false)">+ حالة</button>
                    </div>
                </div>
                <div class="dept-sub-header">
                    <div class="dept-sub-field" style="flex:1;">
                        <i class="fa-solid fa-user-doctor"></i>
                        <span>تسليم إلى:</span>
                        <input type="text" id="doc-women" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:200px;">
                    </div>
                    <div class="dept-sub-field">
                        <i class="fa-solid fa-phone"></i>
                        <span>الهاتف:</span>
                        <input type="tel" id="phone-women" placeholder="رقم الهاتف" oninput="saveData()" style="width:120px;">
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
                            <th style="width: 18%;">اسم المريضة</th>
                            <th style="width: 6%;">العمر</th>
                            <th style="width: 18%;">الطبيب الاختصاص</th>
                            <th style="width: 21%;">التشخيص الطبي</th>
                            <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                            <th class="edit-only" style="width: 5%;"></th>
                        </tr>
                    </thead>
                    <tbody id="women-tbody">
                        <tr>
                            <td>
                                <span class="bed-badge cell-editable" contenteditable="false" oninput="saveData()">Case 1</span>
                                <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                            </td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-darkblue" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable cell-plan" contenteditable="false" oninput="saveData()"></div></td>
                            <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                        </tr>
                    </tbody>
                </table>
                <div class="sub-dept-container" id="sub-container-card-women"></div>
            </div>

            <!-- 3. العناية الباطنية -->
            <div class="dept-card dept-med-icu" id="card-med-icu">
                <div class="dept-banner" id="banner-card-med-icu">
                    <div class="dept-title">
                        <i class="fa-solid fa-heart-pulse"></i>
                        <input type="text" class="dept-title-editable" value="العناية الباطنية (Medical ICU)" oninput="updateTabTitle('card-med-icu', this.value); saveData();" />
                    </div>
                    <div class="dept-actions edit-only">
                        <label class="change-color-btn">
                            <i class="fa-solid fa-palette"></i> لون
                            <input type="color" onchange="changeDeptColor('card-med-icu', this.value)">
                        </label>
                        <button class="btn btn-add-subdept" onclick="addSubDepartment('card-med-icu')">+ قسم فرعي</button>
                        <button class="btn btn-add-patient" onclick="addRow('med-icu-tbody', false)">+ حالة</button>
                    </div>
                </div>
                <div class="dept-sub-header">
                    <div class="dept-sub-field" style="flex:1;">
                        <i class="fa-solid fa-user-doctor"></i>
                        <span>تسليم إلى:</span>
                        <input type="text" id="doc-med-icu" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:200px;">
                    </div>
                    <div class="dept-sub-field">
                        <i class="fa-solid fa-phone"></i>
                        <span>الهاتف:</span>
                        <input type="tel" id="phone-med-icu" placeholder="رقم الهاتف" oninput="saveData()" style="width:120px;">
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
                            <th style="width: 18%;">اسم المريض</th>
                            <th style="width: 6%;">العمر</th>
                            <th style="width: 18%;">الطبيب الاختصاص</th>
                            <th style="width: 21%;">التشخيص الطبي</th>
                            <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                            <th class="edit-only" style="width: 5%;"></th>
                        </tr>
                    </thead>
                    <tbody id="med-icu-tbody">
                        <tr>
                            <td>
                                <span class="bed-badge cell-editable" contenteditable="false" oninput="saveData()">Case 1</span>
                                <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                            </td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">محمد نزار</div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">58</div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()">د مصعب</div></td>
                            <td><div class="cell-editable text-darkblue" contenteditable="false" oninput="saveData()">IHD</div></td>
                            <td><div class="cell-editable cell-plan" contenteditable="false" oninput="saveData()">متابعة العلاج الذاتي</div></td>
                            <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                        </tr>
                    </tbody>
                </table>
                <div class="sub-dept-container" id="sub-container-card-med-icu"></div>
            </div>

            <!-- 4. العناية الجراحية -->
            <div class="dept-card dept-surg-icu" id="card-surg-icu">
                <div class="dept-banner" id="banner-card-surg-icu">
                    <div class="dept-title">
                        <i class="fa-solid fa-scalpel"></i>
                        <input type="text" class="dept-title-editable" value="العناية الجراحية (Surgical ICU)" oninput="updateTabTitle('card-surg-icu', this.value); saveData();" />
                    </div>
                    <div class="dept-actions edit-only">
                        <label class="change-color-btn">
                            <i class="fa-solid fa-palette"></i> لون
                            <input type="color" onchange="changeDeptColor('card-surg-icu', this.value)">
                        </label>
                        <button class="btn btn-add-subdept" onclick="addSubDepartment('card-surg-icu')">+ قسم فرعي</button>
                        <button class="btn btn-add-patient" onclick="addRow('surg-icu-tbody', false)">+ حالة</button>
                    </div>
                </div>
                <div class="dept-sub-header">
                    <div class="dept-sub-field" style="flex:1;">
                        <i class="fa-solid fa-user-doctor"></i>
                        <span>تسليم إلى:</span>
                        <input type="text" id="doc-surg-icu" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:200px;">
                    </div>
                    <div class="dept-sub-field">
                        <i class="fa-solid fa-phone"></i>
                        <span>الهاتف:</span>
                        <input type="tel" id="phone-surg-icu" placeholder="رقم الهاتف" oninput="saveData()" style="width:120px;">
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
                            <th style="width: 18%;">اسم المريض</th>
                            <th style="width: 6%;">العمر</th>
                            <th style="width: 18%;">الطبيب الاختصاص</th>
                            <th style="width: 21%;">التشخيص الطبي</th>
                            <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                            <th class="edit-only" style="width: 5%;"></th>
                        </tr>
                    </thead>
                    <tbody id="surg-icu-tbody">
                        <tr>
                            <td>
                                <span class="bed-badge cell-editable" contenteditable="false" oninput="saveData()">Case 1</span>
                                <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                            </td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-black" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable text-darkblue" contenteditable="false" oninput="saveData()"></div></td>
                            <td><div class="cell-editable cell-plan" contenteditable="false" oninput="saveData()"></div></td>
                            <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                        </tr>
                    </tbody>
                </table>
                <div class="sub-dept-container" id="sub-container-card-surg-icu"></div>
            </div>
        </div>

        <div class="footer-banner-box">
            <div class="footer-title">لتعديل أو تحديث بيانات الخريطة عبر الموقع الإلكتروني:</div>
            <a href="#" target="_blank" id="appLiveUrl">جاري تحميل الرابط...</a>
            <div class="developer-credits">
                Developed by dr. Mohammad Jasim
            </div>
        </div>

    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
        import { getDatabase, ref, set, onValue } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyB09HCMKQctnh1gjMb-qR7jeSPsffkFCu8",
            authDomain: "hospital-patients-handover-map.firebaseapp.com",
            databaseURL: "https://hospital-patients-handover-map-default-rtdb.firebaseio.com",
            projectId: "hospital-patients-handover-map",
            storageBucket: "hospital-patients-handover-map.firebasestorage.app",
            messagingSenderId: "861626853040",
            appId: "1:861626853040:web:03fd42ad26a5d4e45cfd75",
            measurementId: "G-17F9MX1RNR"
        };

        const app = initializeApp(firebaseConfig);
        const database = getDatabase(app);

        let isRemoteUpdate = false;

        window.saveDataToFirebase = function(data) {
            if (isRemoteUpdate || !window.isEditMode) return;
            set(ref(database, 'handoverDataMap_v2'), data);
        };

        const handoverRef = ref(database, 'handoverDataMap_v2');
        onValue(handoverRef, (snapshot) => {
            const data = snapshot.val();
            if (data && window.renderDataFromFirebase && document.activeElement.getAttribute('contenteditable') !== 'true' && document.activeElement.tagName !== 'INPUT') {
                isRemoteUpdate = true;
                window.renderDataFromFirebase(data);
                isRemoteUpdate = false;
            }
        });
    </script>

    <script>
        const APP_PASSWORD = "1020";
        window.isEditMode = false;
        let activeGcsTarget = null;
        let activePatientRow = null;

        function openAuthModal() {
            if (window.isEditMode) {
                setEditMode(false);
            } else {
                document.getElementById('authModal').style.display = 'flex';
                document.getElementById('passInput').focus();
            }
        }

        function closeAuthModal() {
            document.getElementById('authModal').style.display = 'none';
            document.getElementById('passInput').value = '';
            document.getElementById('errMsg').style.display = 'none';
        }

        function checkPass() {
            const inputVal = document.getElementById('passInput').value;
            if (inputVal === APP_PASSWORD) {
                closeAuthModal();
                setEditMode(true);
            } else {
                document.getElementById('errMsg').style.display = 'block';
            }
        }

        /* GCS Modal Functions */
        window.openGCSModal = function(btn) {
            activeGcsTarget = btn.closest('.gcs-compact-badge');
            if(!activeGcsTarget) return;

            const e = activeGcsTarget.getAttribute('data-e') || "4";
            const v = activeGcsTarget.getAttribute('data-v') || "5";
            const m = activeGcsTarget.getAttribute('data-m') || "6";

            document.getElementById('modalGcsE').value = e;
            document.getElementById('modalGcsV').value = v;
            document.getElementById('modalGcsM').value = m;

            calcModalGCS();
            document.getElementById('gcsModal').style.display = 'flex';
        };

        window.closeGCSModal = function() {
            document.getElementById('gcsModal').style.display = 'none';
            activeGcsTarget = null;
        };

        window.calcModalGCS = function() {
            const e = parseInt(document.getElementById('modalGcsE').value || 0);
            const v = parseInt(document.getElementById('modalGcsV').value || 0);
            const m = parseInt(document.getElementById('modalGcsM').value || 0);
            const total = e + v + m;
            document.getElementById('modalGcsTotal').innerText = `${total}/15`;
        };

        window.applyGCSModal = function() {
            if(!activeGcsTarget) return;

            const e = document.getElementById('modalGcsE').value;
            const v = document.getElementById('modalGcsV').value;
            const m = document.getElementById('modalGcsM').value;
            const total = parseInt(e) + parseInt(v) + parseInt(m);

            activeGcsTarget.setAttribute('data-e', e);
            activeGcsTarget.setAttribute('data-v', v);
            activeGcsTarget.setAttribute('data-m', m);

            const valSpan = activeGcsTarget.querySelector('.gcs-val');
            if(valSpan) valSpan.innerText = `${total}/15`;

            closeGCSModal();
            saveData();
        };

        /* --- وظائف مشاركة المريض --- */
        window.openShareModal = function(btn) {
            activePatientRow = btn.closest('tr');
            if(!activePatientRow) return;

            const cells = activePatientRow.querySelectorAll('td');
            let name = cells[1] ? cells[1].innerText.trim() : '';
            let caseNo = cells[0] ? cells[0].querySelector('.bed-badge').innerText.trim() : '';

            document.getElementById('sharePatientName').innerText = `المريض: ${name || caseNo || 'بدون اسم'}`;
            document.getElementById('shareModal').style.display = 'flex';
        };

        window.closeShareModal = function() {
            document.getElementById('shareModal').style.display = 'none';
            activePatientRow = null;
        };

        function getPatientDetailsData() {
            if(!activePatientRow) return null;

            const cells = activePatientRow.querySelectorAll('td');
            const hasGcs = activePatientRow.querySelector('.gcs-compact-badge') !== null;
            
            let deptCard = activePatientRow.closest('.dept-card');
            let deptName = deptCard ? deptCard.querySelector('.dept-title-editable').value : '';

            let caseNo = cells[0].querySelector('.bed-badge').innerText.trim();
            let name = cells[1].innerText.trim();
            let age = cells[2].innerText.trim();
            
            let gcs = '';
            let docIndex = 3, diagIndex = 4, planIndex = 5;

            if(hasGcs) {
                const gcsBadge = cells[3].querySelector('.gcs-val');
                gcs = gcsBadge ? gcsBadge.innerText.trim() : '15/15';
                docIndex = 4; diagIndex = 5; planIndex = 6;
            }

            let doctor = cells[docIndex] ? cells[docIndex].innerText.trim() : '';
            let diag = cells[diagIndex] ? cells[diagIndex].innerText.trim() : '';
            let plan = cells[planIndex] ? cells[planIndex].innerText.trim() : '';

            return { deptName, caseNo, name, age, gcs, doctor, diag, plan };
        }

        window.sharePatientAsText = function() {
            const data = getPatientDetailsData();
            if(!data) return;

            let text = `🏥 *تقرير حالة مريض - ${data.deptName}*\n`;
            text += `📌 *الحالة:* ${data.caseNo}\n`;
            if(data.name) text += `👤 *الاسم:* ${data.name}\n`;
            if(data.age) text += `🎂 *العمر:* ${data.age}\n`;
            if(data.gcs) text += `🧠 *مقياس GCS:* ${data.gcs}\n`;
            if(data.doctor) text += `👨‍⚕️ *الطبيب الاختصاص:* ${data.doctor}\n`;
            if(data.diag) text += `🩺 *التشخيص:* ${data.diag}\n`;
            if(data.plan) text += `📝 *الخطة العلاجية والتفاصيل:* ${data.plan}\n`;

            navigator.clipboard.writeText(text).then(() => {
                alert('تم نسخ تفاصيل المريض كأنص مرتب بنجاح! يمكنك لصقه في WhatsApp الآن.');
                closeShareModal();
            }).catch(err => {
                alert('حدث خطأ أثناء النسخ.');
            });
        };

        window.downloadPatientAsImage = async function() {
            const data = getPatientDetailsData();
            if(!data) return;

            const renderContainer = document.getElementById('singlePatientCardRender');
            renderContainer.innerHTML = `
                <div style="text-align:center; border-bottom:2px solid #0284c7; padding-bottom:10px; margin-bottom:12px;">
                    <h3 style="margin:0; color:#0f172a; font-size:16px;">مستشفى طوارىء ام الربيعين 🌼</h3>
                    <p style="margin:3px 0 0 0; color:#0284c7; font-weight:800; font-size:14px;">${data.deptName} - ${data.caseNo}</p>
                </div>
                <table style="width:100%; border-collapse:collapse; text-align:right;">
                    <tr><td style="padding:6px; font-weight:800; width:35%; border-bottom:1px solid #e2e8f0;">اسم المريض:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0; color:#000;">${data.name || '-'}</td></tr>
                    <tr><td style="padding:6px; font-weight:800; border-bottom:1px solid #e2e8f0;">العمر:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0;">${data.age || '-'}</td></tr>
                    ${data.gcs ? `<tr><td style="padding:6px; font-weight:800; border-bottom:1px solid #e2e8f0;">مقياس الوعي GCS:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0; color:#dc2626; font-weight:800;">${data.gcs}</td></tr>` : ''}
                    <tr><td style="padding:6px; font-weight:800; border-bottom:1px solid #e2e8f0;">الطبيب الاختصاص:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0;">${data.doctor || '-'}</td></tr>
                    <tr><td style="padding:6px; font-weight:800; border-bottom:1px solid #e2e8f0;">التشخيص الطبي:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0; color:#1e3a8a; font-weight:700;">${data.diag || '-'}</td></tr>
                    <tr><td style="padding:6px; font-weight:800; border-bottom:1px solid #e2e8f0;">الخطة العلاجية:</td><td style="padding:6px; border-bottom:1px solid #e2e8f0; color:#0369a1; font-weight:700;">${data.plan || '-'}</td></tr>
                </table>
                <div style="margin-top:12px; text-align:center; font-size:10px; color:#64748b; direction:ltr;">Developed by dr. Mohammad Jasim</div>
            `;

            try {
                const canvas = await html2canvas(renderContainer);
                
                canvas.toBlob(async (blob) => {
                    if (!blob) {
                        alert("حدث خطأ أثناء إنشاء الصورة.");
                        return;
                    }

                    const fileName = `Patient_${data.caseNo}_${data.name || 'Report'}.png`;
                    const file = new File([blob], fileName, { type: 'image/png' });

                    // 1. محاولة المشاركة المباشرة عبر تطبيقات الهاتف (WhatsApp, Telegram, etc.)
                    if (navigator.canShare && navigator.canShare({ files: [file] })) {
                        try {
                            await navigator.share({
                                files: [file],
                                title: `تقرير حالة مريض - ${data.caseNo}`,
                                text: `بطاقة المريض: ${data.name || data.caseNo}`
                            });
                            closeShareModal();
                            return;
                        } catch (shareErr) {
                            // تم إلغاء المشاركة من قبل المستخدم
                        }
                    }

                    // 2. إذا لم تكن ميزة المشاركة مدعومة، يتم فتح الصورة لتنزيلها أو حفظها بالاستوديو
                    const blobUrl = URL.createObjectURL(blob);
                    const link = document.createElement('a');
                    link.href = blobUrl;
                    link.download = fileName;
                    document.body.appendChild(link);
                    link.click();
                    document.body.removeChild(link);
                    
                    closeShareModal();
                }, 'image/png');

            } catch (err) {
                alert("تعذر حفظ الصورة، يرجى المحاولة مرة أخرى.");
            }
        };

        function applyFormat(e, command, value = null) {
            e.preventDefault();
            document.execCommand(command, false, value);
            saveData();
        }

        function applyColor(e, color) {
            e.preventDefault();
            document.execCommand('foreColor', false, color);
            saveData();
        }

        function toggleEditModeFloating() {
            if (window.isEditMode) {
                setEditMode(false);
            } else {
                openAuthModal();
            }
        }

        function setEditMode(enable) {
            window.isEditMode = enable;
            const btn = document.getElementById('toggleEditBtn');
            const floatBtn = document.getElementById('floatingEditBtn');
            const floatIcon = document.getElementById('floatingIcon');

            if (enable) {
                document.body.classList.remove('read-only-mode');
                document.body.classList.add('edit-mode-active');
                btn.innerHTML = `<i class="fa-solid fa-lock-open"></i> وضع التعديل (مُفعل الآن)`;
                btn.style.background = "#16a34a";
                
                floatBtn.classList.add('active');
                floatIcon.className = "fa-solid fa-lock-open";

                document.querySelectorAll('.cell-editable').forEach(cell => cell.setAttribute('contenteditable', 'true'));
            } else {
                document.body.classList.add('read-only-mode');
                document.body.classList.remove('edit-mode-active');
                btn.innerHTML = `<i class="fa-solid fa-lock"></i> وضع التعديل (للأطباء)`;
                btn.style.background = "#0284c7";

                floatBtn.classList.remove('active');
                floatIcon.className = "fa-solid fa-lock";

                document.querySelectorAll('.cell-editable').forEach(cell => cell.setAttribute('contenteditable', 'false'));
            }
        }

        document.getElementById('appLiveUrl').href = window.location.href;
        document.getElementById('appLiveUrl').innerText = window.location.href;

        let saveTimeout = null;

        function changeFontFamily(font) {
            document.documentElement.style.setProperty('--main-font', font);
            saveData();
        }

        function changeDeptColor(cardId, color) {
            const banner = document.getElementById(`banner-${cardId}`);
            const tabBtn = document.getElementById(`tab-btn-${cardId}`);
            if(banner) banner.style.backgroundColor = color;
            if(tabBtn) tabBtn.style.backgroundColor = color;
            saveData();
        }

        function changeSubDeptColor(subId, color) {
            const banner = document.getElementById(`sub-banner-${subId}`);
            if(banner) banner.style.backgroundColor = color;
            saveData();
        }

        function updateTabTitle(cardId, newTitle) {
            const label = document.getElementById(`label-${cardId}`);
            if(label) label.innerText = newTitle || 'قسم بدون عنوان';
        }

        function showTab(targetCardId, btn) {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');

            const cards = document.querySelectorAll('.dept-card');
            cards.forEach(card => {
                if (targetCardId === 'all' || card.id === targetCardId) {
                    card.style.display = 'block';
                } else {
                    card.style.display = 'none';
                }
            });
        }

        window.addSubDepartment = function(parentCardId) {
            const container = document.getElementById(`sub-container-${parentCardId}`);
            if (!container) return;

            const subId = 'sub-' + Date.now();
            const tbodyId = `${subId}-tbody`;
            const isEditable = window.isEditMode ? 'true' : 'false';

            const subDeptHTML = `
                <div class="sub-dept-card" id="${subId}">
                    <div class="sub-dept-banner" id="sub-banner-${subId}" style="background-color: #475569;">
                        <div style="display:flex; align-items:center; gap:6px; flex:1;">
                            <i class="fa-solid fa-layer-group"></i>
                            <input type="text" class="sub-dept-title-editable" value="قسم فرعي جديد" oninput="saveData()" />
                        </div>
                        <div class="dept-actions edit-only">
                            <label class="change-color-btn">
                                <i class="fa-solid fa-palette"></i> لون
                                <input type="color" value="#475569" onchange="changeSubDeptColor('${subId}', this.value)">
                            </label>
                            <button class="btn btn-add-patient" onclick="addRow('${tbodyId}', false)">+ حالة</button>
                            <button class="delete-dept-btn" onclick="removeSubDepartment('${subId}')"><i class="fa-solid fa-trash"></i> حذف</button>
                        </div>
                    </div>
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 10%;">الحالة</th>
                                <th style="width: 18%;">اسم المريض</th>
                                <th style="width: 6%;">العمر</th>
                                <th style="width: 18%;">الطبيب الاختصاص</th>
                                <th style="width: 21%;">التشخيص الطبي</th>
                                <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                                <th class="edit-only" style="width: 5%;"></th>
                            </tr>
                        </thead>
                        <tbody id="${tbodyId}">
                            <tr>
                                <td>
                                    <span class="bed-badge cell-editable" contenteditable="${isEditable}" oninput="saveData()">Case 1</span>
                                    <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                                </td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-darkblue" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable cell-plan" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            `;

            container.insertAdjacentHTML('beforeend', subDeptHTML);
            setEditMode(window.isEditMode);
            saveData();
        };

        window.removeSubDepartment = function(subId) {
            if (confirm('هل أنت متأكد من حذف هذا القسم الفرعي بالكامل؟')) {
                const el = document.getElementById(subId);
                if (el) el.remove();
                saveData();
            }
        };

        window.addNewDepartment = function() {
            const wrapper = document.getElementById('departmentsWrapper');
            const tabsContainer = document.getElementById('tabsContainer');
            if (!wrapper) return;

            const deptId = 'card-custom-' + Date.now();
            const tbodyId = `${deptId}-tbody`;
            const subContainerId = `sub-container-${deptId}`;
            const isEditable = window.isEditMode ? 'true' : 'false';

            const deptHTML = `
                <div class="dept-card" id="${deptId}">
                    <div class="dept-banner" id="banner-${deptId}" style="background-color: #0284c7;">
                        <div class="dept-title">
                            <i class="fa-solid fa-hospital-user"></i>
                            <input type="text" class="dept-title-editable" value="قسم رئيسي جديد" oninput="updateTabTitle('${deptId}', this.value); saveData();" />
                        </div>
                        <div class="dept-actions edit-only">
                            <label class="change-color-btn">
                                <i class="fa-solid fa-palette"></i> لون
                                <input type="color" value="#0284c7" onchange="changeDeptColor('${deptId}', this.value)">
                            </label>
                            <button class="btn btn-add-subdept" onclick="addSubDepartment('${deptId}')">+ قسم فرعي</button>
                            <button class="btn btn-add-patient" onclick="addRow('${tbodyId}', false)">+ حالة</button>
                            <button class="delete-dept-btn" onclick="removeDepartment('${deptId}')"><i class="fa-solid fa-trash"></i> حذف</button>
                        </div>
                    </div>
                    <div class="dept-sub-header">
                        <div class="dept-sub-field" style="flex:1;">
                            <i class="fa-solid fa-user-doctor"></i>
                            <span>تسليم إلى:</span>
                            <input type="text" placeholder="اسم الطبيب المستلم" oninput="saveData()" style="width:100%; max-width:200px;">
                        </div>
                        <div class="dept-sub-field">
                            <i class="fa-solid fa-phone"></i>
                            <span>الهاتف:</span>
                            <input type="tel" placeholder="رقم الهاتف" oninput="saveData()" style="width:120px;">
                        </div>
                        <div class="dept-sub-field">
                            <i class="fa-solid fa-calendar-day"></i>
                            <select onchange="saveData()">
                                <option>السبت</option><option>الأحد</option><option>الإثنين</option><option>الثلاثاء</option><option>الأربعاء</option><option>الخميس</option><option>الجمعة</option>
                            </select>
                        </div>
                        <div class="dept-sub-field">
                            <i class="fa-solid fa-calendar-check"></i>
                            <input type="date" onchange="saveData()">
                        </div>
                    </div>
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 10%;">الحالة</th>
                                <th style="width: 18%;">اسم المريض</th>
                                <th style="width: 6%;">العمر</th>
                                <th style="width: 18%;">الطبيب الاختصاص</th>
                                <th style="width: 21%;">التشخيص الطبي</th>
                                <th style="width: 22%;">الخطة العلاجية والتفاصيل</th>
                                <th class="edit-only" style="width: 5%;"></th>
                            </tr>
                        </thead>
                        <tbody id="${tbodyId}">
                            <tr>
                                <td>
                                    <span class="bed-badge cell-editable" contenteditable="${isEditable}" oninput="saveData()">Case 1</span>
                                    <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                                </td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable text-darkblue" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td><div class="cell-editable cell-plan" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                                <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                            </tr>
                        </tbody>
                    </table>
                    <div class="sub-dept-container" id="${subContainerId}"></div>
                </div>
            `;

            wrapper.insertAdjacentHTML('beforeend', deptHTML);

            if (tabsContainer) {
                const tabBtnHTML = `
                    <button class="tab-btn" id="tab-btn-${deptId}" style="background-color: #0284c7;" onclick="showTab('${deptId}', this)">
                        <i class="fa-solid fa-hospital-user"></i> <span id="label-${deptId}">قسم رئيسي جديد</span>
                    </button>
                `;
                tabsContainer.insertAdjacentHTML('beforeend', tabBtnHTML);
            }

            setEditMode(window.isEditMode);
            saveData();
        };

        window.removeDepartment = function(deptId) {
            if (confirm('هل أنت متأكد من حذف هذا القسم الرئيسي بجميع أقسامه الفرعية وحالاته؟')) {
                const deptEl = document.getElementById(deptId);
                const tabEl = document.getElementById(`tab-btn-${deptId}`);
                if (deptEl) deptEl.remove();
                if (tabEl) tabEl.remove();
                saveData();
            }
        };

        function addRow(tbodyId, hasGCS = false) {
            const tbody = document.getElementById(tbodyId);
            if(!tbody) return;
            const count = tbody.children.length + 1;
            const isEditable = window.isEditMode ? 'true' : 'false';
            const tr = document.createElement('tr');

            if(hasGCS) {
                tr.innerHTML = `
                    <td>
                        <span class="bed-badge cell-editable" contenteditable="${isEditable}" oninput="saveData()">Case ${count}</span>
                        <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                    </td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td>
                        <div class="gcs-compact-badge" data-e="4" data-v="5" data-m="6">
                            <span>GCS: <strong class="gcs-val">15/15</strong></span>
                            <button class="gcs-calc-btn edit-only" onclick="openGCSModal(this)" title="إعادة حساب GCS">🧮</button>
                        </div>
                    </td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable text-darkblue" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable cell-plan" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                `;
            } else {
                tr.innerHTML = `
                    <td>
                        <span class="bed-badge cell-editable" contenteditable="${isEditable}" oninput="saveData()">Case ${count}</span>
                        <button class="btn-share-patient" onclick="openShareModal(this)"><i class="fa-solid fa-share-nodes"></i> مشاركة</button>
                    </td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable text-black" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable text-darkblue" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td><div class="cell-editable cell-plan" contenteditable="${isEditable}" oninput="saveData()"></div></td>
                    <td class="edit-only" style="text-align: center;"><i class="fa-solid fa-trash-can delete-btn" onclick="deleteRow(this)"></i></td>
                `;
            }
            tbody.appendChild(tr);
            saveData();
        }

        function deleteRow(btn) {
            btn.closest('tr').remove();
            saveData();
        }

        function updateInputAttributes() {
            const wrapper = document.getElementById('departmentsWrapper');
            if (!wrapper) return;
            
            wrapper.querySelectorAll('input').forEach(input => {
                if (input.type !== 'file' && input.type !== 'button') {
                    input.setAttribute('value', input.value);
                }
            });

            wrapper.querySelectorAll('select').forEach(select => {
                const val = select.value;
                select.querySelectorAll('option').forEach(opt => {
                    if (opt.value === val || opt.text === val) {
                        opt.setAttribute('selected', 'selected');
                    } else {
                        opt.removeAttribute('selected');
                    }
                });
            });
        }

        function saveData() {
            if (!window.isEditMode) return;
            
            clearTimeout(saveTimeout);
            saveTimeout = setTimeout(() => {
                updateInputAttributes();

                const fontSelect = document.querySelector('.font-select-control');
                const data = {
                    selectedFont: fontSelect ? fontSelect.value : "'Cairo', sans-serif",
                    mainDay: document.getElementById('mainDaySelect').value,
                    mainDate: document.getElementById('mainDateInput').value,
                    htmlContent: document.getElementById('departmentsWrapper').innerHTML,
                    tabsContent: document.getElementById('tabsContainer').innerHTML
                };
                
                localStorage.setItem('handoverDataMap_v2', JSON.stringify(data));

                if (window.saveDataToFirebase) {
                    window.saveDataToFirebase(data);
                }
            }, 300);
        }

        window.renderDataFromFirebase = function(data) {
            if (!data) return;

            if(data.selectedFont) {
                const fontSelect = document.querySelector('.font-select-control');
                if(fontSelect) fontSelect.value = data.selectedFont;
                document.documentElement.style.setProperty('--main-font', data.selectedFont);
            }

            if(data.mainDay) document.getElementById('mainDaySelect').value = data.mainDay;
            if(data.mainDate) document.getElementById('mainDateInput').value = data.mainDate;

            if(data.htmlContent) document.getElementById('departmentsWrapper').innerHTML = data.htmlContent;
            if(data.tabsContent) document.getElementById('tabsContainer').innerHTML = data.tabsContent;

            setEditMode(window.isEditMode);
        };

        function loadData() {
            const saved = localStorage.getItem('handoverDataMap_v2');
            const todayStr = new Date().toISOString().split('T')[0];

            if (saved) {
                renderDataFromFirebase(JSON.parse(saved));
            } else {
                document.getElementById('mainDateInput').value = todayStr;
                document.getElementById('date-men').value = todayStr;
                document.getElementById('date-women').value = todayStr;
                document.getElementById('date-med-icu').value = todayStr;
                document.getElementById('date-surg-icu').value = todayStr;
            }
        }

        function clearData() {
            if (confirm('هل أنت متأكد من مسح جميع البيانات والبدء من جديد؟')) {
                localStorage.removeItem('handoverDataMap_v2');
                if(window.saveDataToFirebase) window.saveDataToFirebase({});
                location.reload();
            }
        }

        function generatePDF() { window.print(); }

        window.onload = loadData;
    </script>
</body>
</html>
