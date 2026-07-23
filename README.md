<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مستشفى طوارئ أم الربيعين - خريطة تسليم المرضى</title>

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
<body>

    <!-- أدخل باقي عناصر الـ HTML الخاصة بالواجهة هنا -->

</body>
</html>
