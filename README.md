<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>قراءة بطاقة الرقم القومي - مصر</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@v5/dist/tesseract.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            background: linear-gradient(135deg, #1a3a1a 0%, #2d5a2d 100%);
            color: #333;
            min-height: 100vh;
            padding: 10px;
            overflow-x: hidden;
        }
        
        .container {
            max-width: 100%;
            margin: 0 auto;
            background-color: rgba(255, 255, 255, 0.98);
            border-radius: 15px;
            box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2);
            overflow: hidden;
            padding-bottom: 20px;
        }
        
        header {
            background: linear-gradient(to right, #c19a6b, #8b6914);
            color: white;
            padding: 20px 15px;
            text-align: center;
            border-bottom: 5px solid #5d432c;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        
        header h1 {
            font-size: 1.6rem;
            margin-bottom: 8px;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
        }
        
        header p {
            font-size: 0.95rem;
            opacity: 0.9;
            line-height: 1.4;
        }
        
        .main-content {
            display: flex;
            flex-direction: column;
            padding: 15px;
            gap: 20px;
        }
        
        .camera-section, .data-section, .excel-section {
            width: 100%;
            background-color: #f8f5f0;
            border-radius: 12px;
            padding: 15px;
            box-shadow: 0 3px 10px rgba(0, 0, 0, 0.08);
        }
        
        .section-title {
            color: #5d432c;
            border-bottom: 2px solid #c19a6b;
            padding-bottom: 8px;
            margin-bottom: 15px;
            font-size: 1.3rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .section-title i {
            color: #8b6914;
            font-size: 1.2rem;
        }
        
        .video-container {
            width: 100%;
            background-color: #000;
            border-radius: 8px;
            overflow: hidden;
            margin-bottom: 12px;
            position: relative;
            aspect-ratio: 4/3;
        }
        
        #video {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transform: rotateY(180deg); /* مرآة للكاميرا الأمامية */
        }
        
        .camera-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: 3px dashed rgba(193, 154, 107, 0.7);
            pointer-events: none;
        }
        
        .camera-controls {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            justify-content: center;
            margin-top: 10px;
        }
        
        button {
            padding: 14px 20px;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            flex: 1;
            min-width: 140px;
            min-height: 54px;
            touch-action: manipulation;
        }
        
        button:active {
            transform: scale(0.98);
        }
        
        .btn-primary {
            background-color: #8b6914;
            color: white;
        }
        
        .btn-primary:hover, .btn-primary:active {
            background-color: #6d5010;
        }
        
        .btn-secondary {
            background-color: #5d432c;
            color: white;
        }
        
        .btn-secondary:hover, .btn-secondary:active {
            background-color: #47321f;
        }
        
        .btn-success {
            background-color: #2d5a2d;
            color: white;
        }
        
        .btn-success:hover, .btn-success:active {
            background-color: #1f3f1f;
        }
        
        .captured-image-container {
            margin-top: 15px;
            display: none;
        }
        
        #capturedImage {
            width: 100%;
            border-radius: 8px;
            border: 3px solid #c19a6b;
            max-height: 300px;
            object-fit: contain;
            background-color: #000;
        }
        
        .data-form {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }
        
        .form-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        
        label {
            font-weight: 600;
            color: #5d432c;
            font-size: 0.95rem;
        }
        
        input, textarea {
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 1rem;
            transition: border 0.3s;
            width: 100%;
            -webkit-appearance: none;
        }
        
        input:focus, textarea:focus {
            border-color: #8b6914;
            outline: none;
        }
        
        textarea {
            min-height: 90px;
            resize: vertical;
        }
        
        .ocr-progress {
            margin-top: 15px;
            display: none;
        }
        
        .progress-bar {
            width: 100%;
            height: 16px;
            background-color: #e0e0e0;
            border-radius: 8px;
            overflow: hidden;
            margin-bottom: 8px;
        }
        
        .progress-fill {
            height: 100%;
            background: linear-gradient(to right, #8b6914, #c19a6b);
            width: 0%;
            transition: width 0.5s ease;
        }
        
        .unit-number-section {
            background-color: #e8f4e8;
            border-radius: 10px;
            padding: 12px;
            margin-top: 12px;
            border-left: 5px solid #2d5a2d;
        }
        
        .unit-number-section h3 {
            color: #1f3f1f;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 1.1rem;
        }
        
        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            display: none;
            font-size: 0.9rem;
            overflow-x: auto;
            display: block;
        }
        
        .data-table th, .data-table td {
            border: 1px solid #c19a6b;
            padding: 8px;
            text-align: right;
            white-space: nowrap;
        }
        
        .data-table th {
            background-color: #8b6914;
            color: white;
            font-size: 0.9rem;
        }
        
        .data-table tr:nth-child(even) {
            background-color: #f5f0e6;
        }
        
        .export-controls {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 15px;
        }
        
        .instructions {
            background-color: #f0e6d6;
            border-radius: 10px;
            padding: 12px;
            margin-top: 15px;
            border-right: 5px solid #8b6914;
            font-size: 0.9rem;
        }
        
        .instructions h3 {
            color: #5d432c;
            margin-bottom: 8px;
            font-size: 1.1rem;
        }
        
        .instructions ol, .instructions ul {
            padding-right: 18px;
        }
        
        .instructions li {
            margin-bottom: 6px;
            line-height: 1.4;
        }
        
        .status-message {
            padding: 12px;
            border-radius: 8px;
            margin: 12px 0;
            text-align: center;
            font-weight: 600;
            display: none;
            font-size: 0.95rem;
        }
        
        .success {
            background-color: #e8f5e8;
            color: #2d5a2d;
            border: 1px solid #2d5a2d;
        }
        
        .error {
            background-color: #fde8e8;
            color: #a52a2a;
            border: 1px solid #a52a2a;
        }
        
        .processing {
            background-color: #fff8e1;
            color: #8b6914;
            border: 1px solid #8b6914;
        }
        
        .image-controls {
            margin-top: 12px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        
        .slider-container {
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }
        
        .slider-container label {
            min-width: 70px;
            font-size: 0.9rem;
        }
        
        input[type="range"] {
            flex-grow: 1;
            height: 10px;
            border-radius: 5px;
            background: #ddd;
            outline: none;
            min-width: 150px;
        }
        
        .preview-buttons {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }
        
        .mobile-optimized-buttons {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-top: 15px;
        }
        
        .mobile-optimized-buttons button {
            min-height: 60px;
            font-size: 1.1rem;
        }
        
        .camera-toggle {
            position: absolute;
            top: 15px;
            left: 15px;
            background: rgba(0, 0, 0, 0.6);
            color: white;
            border: none;
            border-radius: 50%;
            width: 45px;
            height: 45px;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 10;
            cursor: pointer;
            font-size: 1.2rem;
        }
        
        .flash-toggle {
            position: absolute;
            top: 15px;
            right: 15px;
            background: rgba(0, 0, 0, 0.6);
            color: white;
            border: none;
            border-radius: 50%;
            width: 45px;
            height: 45px;
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 10;
            cursor: pointer;
            font-size: 1.2rem;
        }
        
        .tabs {
            display: flex;
            background-color: #e8e0d4;
            border-radius: 10px;
            margin-bottom: 15px;
            overflow: hidden;
        }
        
        .tab {
            flex: 1;
            padding: 12px;
            text-align: center;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s;
            color: #5d432c;
            border-bottom: 3px solid transparent;
        }
        
        .tab.active {
            background-color: #8b6914;
            color: white;
            border-bottom: 3px solid #5d432c;
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        footer {
            text-align: center;
            padding: 15px;
            color: white;
            margin-top: 20px;
            font-size: 0.8rem;
            opacity: 0.8;
        }
        
        /* تحسينات للشاشات الصغيرة جداً */
        @media (max-width: 480px) {
            header h1 {
                font-size: 1.4rem;
            }
            
            header p {
                font-size: 0.85rem;
            }
            
            .section-title {
                font-size: 1.2rem;
            }
            
            button {
                padding: 12px 15px;
                font-size: 0.95rem;
                min-width: 120px;
            }
            
            .mobile-optimized-buttons button {
                min-height: 55px;
                font-size: 1rem;
            }
            
            .data-table {
                font-size: 0.8rem;
            }
            
            .data-table th, .data-table td {
                padding: 6px;
            }
        }
        
        /* تحسينات للوضع الأفقي */
        @media (min-width: 768px) and (orientation: landscape) {
            .main-content {
                flex-direction: row;
                flex-wrap: wrap;
            }
            
            .camera-section {
                width: 100%;
            }
            
            .data-section, .excel-section {
                width: calc(50% - 10px);
            }
        }
        
        /* تحسينات للأجهزة الكبيرة */
        @media (min-width: 1024px) {
            .container {
                max-width: 1200px;
                margin: 0 auto;
            }
            
            .main-content {
                flex-direction: row;
                flex-wrap: wrap;
            }
            
            .camera-section {
                width: calc(50% - 10px);
            }
            
            .data-section, .excel-section {
                width: calc(50% - 10px);
            }
        }
        
        /* إزالة تأثير التحديد على الأزرار */
        button, input, textarea, select {
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }
        
        /* تحسينات للشاشات التي تدعم hover */
        @media (hover: hover) {
            button:hover {
                transform: translateY(-2px);
                box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-id-card"></i> قارئ بطاقة الرقم القومي</h1>
            <p>اقرأ بطاقة الرقم القومي المصري بكاميرا هاتفك واحفظ البيانات في إكسل</p>
        </header>
        
        <!-- علامات التبويب للجوال -->
        <div class="tabs">
            <div class="tab active" data-tab="camera-tab">الكاميرا</div>
            <div class="tab" data-tab="data-tab">البيانات</div>
            <div class="tab" data-tab="excel-tab">الإكسل</div>
        </div>
        
        <div class="main-content">
            <!-- قسم الكاميرا -->
            <section class="camera-section tab-content active" id="camera-tab">
                <h2 class="section-title"><i class="fas fa-camera"></i> التصوير</h2>
                <div class="video-container">
                    <video id="video" autoplay playsinline muted></video>
                    <div class="camera-overlay"></div>
                    <button class="camera-toggle" id="cameraToggle" title="تبديل الكاميرا">
                        <i class="fas fa-camera-retro"></i>
                    </button>
                    <button class="flash-toggle" id="flashToggle" title="فلاش">
                        <i class="fas fa-bolt"></i>
                    </button>
                </div>
                
                <div class="mobile-optimized-buttons">
                    <button id="startCamera" class="btn-primary">
                        <i class="fas fa-play"></i> تشغيل الكاميرا
                    </button>
                    <button id="captureBtn" class="btn-secondary" disabled>
                        <i class="fas fa-camera"></i> التقاط صورة
                    </button>
                    <button id="resetCamera" class="btn-secondary">
                        <i class="fas fa-redo"></i> إعادة تشغيل
                    </button>
                </div>
                
                <div class="captured-image-container">
                    <h3 class="section-title"><i class="fas fa-image"></i> الصورة الملتقطة</h3>
                    <img id="capturedImage" alt="الصورة الملتقطة للبطاقة">
                    
                    <div class="image-controls">
                        <div class="slider-container">
                            <label for="contrast">التباين:</label>
                            <input type="range" id="contrast" min="50" max="200" value="100">
                            <span id="contrastValue">100%</span>
                        </div>
                        
                        <div class="slider-container">
                            <label for="brightness">السطوع:</label>
                            <input type="range" id="brightness" min="50" max="200" value="100">
                            <span id="brightnessValue">100%</span>
                        </div>
                        
                        <div class="preview-buttons">
                            <button id="applyFilters" class="btn-primary">
                                <i class="fas fa-sliders-h"></i> تطبيق التعديلات
                            </button>
                            <button id="resetFilters" class="btn-secondary">
                                <i class="fas fa-undo"></i> إعادة الضبط
                            </button>
                        </div>
                    </div>
                </div>
                
                <div class="ocr-progress">
                    <h3 class="section-title"><i class="fas fa-cogs"></i> معالجة الصورة</h3>
                    <div class="progress-bar">
                        <div class="progress-fill" id="progressFill"></div>
                    </div>
                    <p id="progressText">جاري تحليل الصورة...</p>
                </div>
                
                <div class="status-message" id="statusMessage"></div>
            </section>
            
            <!-- قسم البيانات -->
            <section class="data-section tab-content" id="data-tab">
                <h2 class="section-title"><i class="fas fa-user"></i> البيانات المستخرجة</h2>
                
                <form class="data-form">
                    <div class="form-group">
                        <label for="name">الاسم بالكامل:</label>
                        <input type="text" id="name" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة">
                    </div>
                    
                    <div class="form-group">
                        <label for="nationalId">الرقم القومي:</label>
                        <input type="text" id="nationalId" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة" maxlength="14" inputmode="numeric">
                    </div>
                    
                    <div class="form-group">
                        <label for="address">العنوان:</label>
                        <textarea id="address" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة"></textarea>
                    </div>
                    
                    <div class="unit-number-section">
                        <h3><i class="fas fa-hashtag"></i> رقم الوحدة (Unit Number)</h3>
                        <div class="form-group">
                            <label for="unitNumber">الرجاء إدخال رقم الوحدة يدوياً:</label>
                            <input type="text" id="unitNumber" placeholder="أدخل رقم الوحدة هنا" required inputmode="numeric">
                        </div>
                    </div>
                    
                    <button type="button" id="processImage" class="btn-success" disabled>
                        <i class="fas fa-barcode"></i> استخراج البيانات من الصورة
                    </button>
                </form>
                
                <div class="instructions">
                    <h3><i class="fas fa-info-circle"></i> تعليمات الاستخدام:</h3>
                    <ol>
                        <li>اضغط على "تشغيل الكاميرا" للسماح بالوصول إلى الكاميرا</li>
                        <li>وجه الكاميرا نحو بطاقة الرقم القومي بحيث تكون واضحة</li>
                        <li>اضغط على "التقاط صورة" لالتقاط صورة البطاقة</li>
                        <li>اضغط على "استخراج البيانات من الصورة" لقراءة البيانات</li>
                        <li>أدخل رقم الوحدة (Unit Number) يدوياً</li>
                        <li>اضغط على "تصدير لملف إكسل" لحفظ البيانات</li>
                    </ol>
                </div>
            </section>
            
            <!-- قسم الإكسل -->
            <section class="excel-section tab-content" id="excel-tab">
                <h2 class="section-title"><i class="fas fa-file-excel"></i> بيانات الإكسل</h2>
                
                <table class="data-table" id="dataTable">
                    <thead>
                        <tr>
                            <th>الاسم</th>
                            <th>الرقم القومي</th>
                            <th>العنوان</th>
                            <th>رقم الوحدة</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                        <!-- البيانات ستضاف هنا ديناميكياً -->
                    </tbody>
                </table>
                
                <div class="mobile-optimized-buttons">
                    <button id="exportExcel" class="btn-success">
                        <i class="fas fa-download"></i> تصدير لملف إكسل
                    </button>
                    <button id="clearData" class="btn-secondary">
                        <i class="fas fa-trash"></i> مسح البيانات
                    </button>
                </div>
                
                <div class="instructions">
                    <h3><i class="fas fa-lightbulb"></i> ملاحظات:</h3>
                    <ul>
                        <li>سيتم حفظ ملف الإكسل باسم "بطاقات_الرقم_القومي_تاريخ.xlsx"</li>
                        <li>يمكنك إضافة عدة بطاقات ثم تصديرها جميعاً مرة واحدة</li>
                        <li>لقراءة بطاقة جديدة، اضغط على "إعادة تشغيل" ثم كرر الخطوات</li>
                        <li>دقة قراءة النصوص تعتمد على جودة الصورة وضوحها</li>
                        <li>استخدم أزرار التباين والسطوع لتحسين جودة الصورة قبل المعالجة</li>
                    </ul>
                </div>
            </section>
        </div>
    </div>
    
    <footer>
        <p>تم التطوير باستخدام HTML5 و Tesseract.js و SheetJS | برنامج قراءة بطاقة الرقم القومي المصري</p>
        <p style="margin-top: 5px;">متوافق مع جميع الأجهزة المحمولة</p>
    </footer>

    <script>
        // عناصر DOM
        const video = document.getElementById('video');
        const capturedImage = document.getElementById('capturedImage');
        const capturedImageContainer = document.querySelector('.captured-image-container');
        const startCameraBtn = document.getElementById('startCamera');
        const captureBtn = document.getElementById('captureBtn');
        const resetCameraBtn = document.getElementById('resetCamera');
        const processImageBtn = document.getElementById('processImage');
        const exportExcelBtn = document.getElementById('exportExcel');
        const clearDataBtn = document.getElementById('clearData');
        const progressFill = document.getElementById('progressFill');
        const progressText = document.getElementById('progressText');
        const ocrProgress = document.querySelector('.ocr-progress');
        const statusMessage = document.getElementById('statusMessage');
        const contrastSlider = document.getElementById('contrast');
        const brightnessSlider = document.getElementById('brightness');
        const contrastValue = document.getElementById('contrastValue');
        const brightnessValue = document.getElementById('brightnessValue');
        const applyFiltersBtn = document.getElementById('applyFilters');
        const resetFiltersBtn = document.getElementById('resetFilters');
        const cameraToggle = document.getElementById('cameraToggle');
        const flashToggle = document.getElementById('flashToggle');
        const tabs = document.querySelectorAll('.tab');
        const tabContents = document.querySelectorAll('.tab-content');
        
        // حقول البيانات
        const nameField = document.getElementById('name');
        const nationalIdField = document.getElementById('nationalId');
        const addressField = document.getElementById('address');
        const unitNumberField = document.getElementById('unitNumber');
        
        // جدول البيانات
        const dataTable = document.getElementById('dataTable');
        const tableBody = document.getElementById('tableBody');
        
        // بيانات التطبيق
        let stream = null;
        let capturedImageData = null;
        let processedImageData = null;
        let cardsData = [];
        let contrast = 100;
        let brightness = 100;
        let facingMode = 'environment'; // الكاميرا الخلفية افتراضياً
        let isFlashOn = false;
        let currentTab = 'camera-tab';
        
        // إدارة علامات التبويب للجوال
        tabs.forEach(tab => {
            tab.addEventListener('click', () => {
                const tabId = tab.getAttribute('data-tab');
                
                // تحديث التبويب النشط
                tabs.forEach(t => t.classList.remove('active'));
                tab.classList.add('active');
                
                // إظهار المحتوى المناسب
                tabContents.forEach(content => {
                    content.classList.remove('active');
                    if (content.id === tabId) {
                        content.classList.add('active');
                    }
                });
                
                currentTab = tabId;
            });
        });
        
        // تحديث قيم السلايدر
        contrastSlider.addEventListener('input', () => {
            contrast = contrastSlider.value;
            contrastValue.textContent = `${contrast}%`;
        });
        
        brightnessSlider.addEventListener('input', () => {
            brightness = brightnessSlider.value;
            brightnessValue.textContent = `${brightness}%`;
        });
        
        // تطبيق التعديلات على الصورة
        applyFiltersBtn.addEventListener('click', () => {
            if (!capturedImageData) {
                showStatus('لا توجد صورة لتطبيق التعديلات عليها.', 'error');
                return;
            }
            
            applyImageFilters();
            showStatus('تم تطبيق التعديلات على الصورة.', 'success');
        });
        
        // إعادة تعيين التعديلات
        resetFiltersBtn.addEventListener('click', () => {
            contrastSlider.value = 100;
            brightnessSlider.value = 100;
            contrast = 100;
            brightness = 100;
            contrastValue.textContent = '100%';
            brightnessValue.textContent = '100%';
            
            if (capturedImageData) {
                capturedImage.src = capturedImageData;
                processedImageData = capturedImageData;
            }
            
            showStatus('تم إعادة تعيين التعديلات.', 'success');
        });
        
        // تبديل الكاميرا (أمامية/خلفية)
        cameraToggle.addEventListener('click', () => {
            if (stream) {
                // إيقاف الكاميرا الحالية
                stream.getTracks().forEach(track => track.stop());
                stream = null;
                
                // تبديل وضع الكاميرا
                facingMode = facingMode === 'environment' ? 'user' : 'environment';
                
                // إعادة تشغيل الكاميرا بالوضع الجديد
                setTimeout(() => {
                    startCamera();
                }, 300);
                
                showStatus(`تم تبديل الكاميرا إلى ${facingMode === 'environment' ? 'الخلفية' : 'الأمامية'}`, 'success');
            }
        });
        
        // تبديل الفلاش (يعمل على بعض الأجهزة فقط)
        flashToggle.addEventListener('click', () => {
            if (stream) {
                const track = stream.getVideoTracks()[0];
                if (track && track.getCapabilities && track.getCapabilities().torch) {
                    isFlashOn = !isFlashOn;
                    track.applyConstraints({
                        advanced: [{torch: isFlashOn}]
                    }).then(() => {
                        flashToggle.innerHTML = isFlashOn ? '<i class="fas fa-bolt-slash"></i>' : '<i class="fas fa-bolt"></i>';
                        showStatus(isFlashOn ? 'تم تشغيل الفلاش' : 'تم إيقاف الفلاش', 'success');
                    }).catch(err => {
                        console.error('خطأ في التحكم بالفلاش:', err);
                        showStatus('هذا الجهاز لا يدعم التحكم بالفلاش', 'error');
                    });
                } else {
                    showStatus('هذا الجهاز لا يدعم التحكم بالفلاش', 'error');
                }
            }
        });
        
        // تطبيق الفلاتر على الصورة
        function applyImageFilters() {
            const canvas = document.createElement('canvas');
            const img = new Image();
            
            img.onload = function() {
                canvas.width = img.width;
                canvas.height = img.height;
                const ctx = canvas.getContext('2d');
                
                // تطبيق التباين والسطوع
                ctx.filter = `contrast(${contrast}%) brightness(${brightness}%)`;
                ctx.drawImage(img, 0, 0);
                
                // تحسين الصورة للقراءة العربية
                const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                const data = imageData.data;
                
                // تحسين التباين للكتابة العربية
                for (let i = 0; i < data.length; i += 4) {
                    // زيادة وضوح النصوص الداكنة
                    const avg = (data[i] + data[i + 1] + data[i + 2]) / 3;
                    
                    // إذا كان اللون قريب من الأسود (نص)
                    if (avg < 128) {
                        // جعل النص أكثر قتامة لزيادة التباين
                        data[i] = Math.max(0, data[i] - 20);     // الأحمر
                        data[i + 1] = Math.max(0, data[i + 1] - 20); // الأخضر
                        data[i + 2] = Math.max(0, data[i + 2] - 20); // الأزرق
                    } else {
                        // تفتيح الخلفية
                        data[i] = Math.min(255, data[i] + 20);     // الأحمر
                        data[i + 1] = Math.min(255, data[i + 1] + 20); // الأخضر
                        data[i + 2] = Math.min(255, data[i + 2] + 20); // الأزرق
                    }
                }
                
                ctx.putImageData(imageData, 0, 0);
                
                // حفظ الصورة المعالجة
                processedImageData = canvas.toDataURL('image/jpeg', 0.9);
                capturedImage.src = processedImageData;
            };
            
            img.src = capturedImageData;
        }
        
        // تشغيل الكاميرا
        function startCamera() {
            startCameraBtn.disabled = true;
            showStatus('جاري تشغيل الكاميرا...', 'processing');
            
            const constraints = {
                video: { 
                    facingMode: facingMode,
                    width: { ideal: 1280 },
                    height: { ideal: 720 }
                } 
            };
            
            navigator.mediaDevices.getUserMedia(constraints)
                .then((mediaStream) => {
                    stream = mediaStream;
                    video.srcObject = stream;
                    captureBtn.disabled = false;
                    showStatus('تم تشغيل الكاميرا بنجاح. قم بتوجيهها نحو بطاقة الرقم القومي.', 'success');
                    
                    // التحقق من دعم الفلاش
                    const track = stream.getVideoTracks()[0];
                    if (track && track.getCapabilities && track.getCapabilities().torch) {
                        flashToggle.style.display = 'flex';
                    } else {
                        flashToggle.style.display = 'none';
                    }
                })
                .catch((err) => {
                    console.error('خطأ في الوصول للكاميرا:', err);
                    startCameraBtn.disabled = false;
                    
                    if (err.name === 'NotAllowedError') {
                        showStatus('تم رفض الوصول إلى الكاميرا. يرجى السماح بالوصول في إعدادات المتصفح.', 'error');
                    } else if (err.name === 'NotFoundError') {
                        showStatus('لم يتم العثور على كاميرا. تأكد من وجود كاميرا في جهازك.', 'error');
                    } else if (err.name === 'NotSupportedError') {
                        showStatus('المتصفح لا يدعم الكاميرا. جرب متصفحاً آخر.', 'error');
                    } else {
                        showStatus('فشل في الوصول للكاميرا: ' + err.message, 'error');
                    }
                });
        }
        
        startCameraBtn.addEventListener('click', startCamera);
        
        // التقاط صورة
        captureBtn.addEventListener('click', () => {
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            
            // تعويض مرآة الفيديو للكاميرا الأمامية
            if (facingMode === 'user') {
                ctx.translate(canvas.width, 0);
                ctx.scale(-1, 1);
            }
            
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            capturedImageData = canvas.toDataURL('image/jpeg', 0.9);
            processedImageData = capturedImageData;
            capturedImage.src = capturedImageData;
            capturedImageContainer.style.display = 'block';
            processImageBtn.disabled = false;
            
            // الانتقال إلى تبويب البيانات بعد التقاط الصورة
            setTimeout(() => {
                document.querySelector('[data-tab="data-tab"]').click();
            }, 500);
            
            // إعادة تعيين التعديلات
            contrastSlider.value = 100;
            brightnessSlider.value = 100;
            contrast = 100;
            brightness = 100;
            contrastValue.textContent = '100%';
            brightnessValue.textContent = '100%';
            
            showStatus('تم التقاط الصورة بنجاح. يمكنك الآن استخراج البيانات.', 'success');
        });
        
        // إعادة تشغيل الكاميرا
        resetCameraBtn.addEventListener('click', () => {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                stream = null;
            }
            
            video.srcObject = null;
            capturedImageContainer.style.display = 'none';
            capturedImageData = null;
            processedImageData = null;
            processImageBtn.disabled = true;
            captureBtn.disabled = true;
            startCameraBtn.disabled = false;
            
            // مسح الحقول
            nameField.value = '';
            nationalIdField.value = '';
            addressField.value = '';
            unitNumberField.value = '';
            
            ocrProgress.style.display = 'none';
            showStatus('تم إعادة تعيين الكاميرا. يمكنك البدء من جديد.', 'success');
            
            // العودة إلى تبويب الكاميرا
            document.querySelector('[data-tab="camera-tab"]').click();
        });
        
        // معالجة الصورة واستخراج البيانات
        processImageBtn.addEventListener('click', async () => {
            if (!capturedImageData) {
                showStatus('لم يتم التقاط صورة بعد. الرجاء التقاط صورة أولاً.', 'error');
                document.querySelector('[data-tab="camera-tab"]').click();
                return;
            }
            
            if (!unitNumberField.value.trim()) {
                showStatus('الرجاء إدخال رقم الوحدة (Unit Number) أولاً.', 'error');
                unitNumberField.focus();
                return;
            }
            
            // استخدام الصورة المعالجة إذا كانت موجودة
            const imageToProcess = processedImageData || capturedImageData;
            
            // إظهار شريط التقدم
            ocrProgress.style.display = 'block';
            progressFill.style.width = '0%';
            progressText.textContent = 'جاري تحميل محرك OCR...';
            processImageBtn.disabled = true;
            
            showStatus('جاري معالجة الصورة واستخراج البيانات... قد تستغرق بضع ثواني', 'processing');
            
            try {
                // تحويل dataURL إلى blob
                const response = await fetch(imageToProcess);
                const blob = await response.blob();
                
                // استخدام Tesseract.js لاستخراج النص مع إعدادات خاصة للعربية
                const worker = await Tesseract.createWorker('ara', 1, {
                    logger: (m) => {
                        if (m.status === 'recognizing text') {
                            const progress = m.progress * 100;
                            progressFill.style.width = `${progress}%`;
                            progressText.textContent = `جاري استخراج النص: ${Math.round(progress)}%`;
                        }
                    },
                    errorHandler: (err) => {
                        console.error('خطأ في Tesseract:', err);
                        showStatus('حدث خطأ أثناء معالجة الصورة.', 'error');
                    }
                });
                
                // إعدادات خاصة لتحسين قراءة النصوص العربية
                await worker.setParameters({
                    preserve_interword_spaces: '1',
                    tessedit_pageseg_mode: '6', // معالجة الصفحة ككتلة واحدة
                    tessedit_char_blacklist: '!@#$%^&*()_+-={}[]|\\:";\'<>?,./`~',
                    textord_min_linesize: '2.5',
                    language_model_ngram_on: '0',
                    tessedit_ocr_engine_mode: '3', // LSTM فقط
                });
                
                // معالجة الصورة
                const { data: { text } } = await worker.recognize(blob, {
                    rotateAuto: true,
                });
                
                await worker.terminate();
                
                progressFill.style.width = '100%';
                progressText.textContent = 'تم استخراج النص بنجاح!';
                
                // تحليل النص المستخرج مع معالجة خاصة للعربية
                const extractedData = parseArabicIDText(text);
                
                // تعبئة الحقول بالبيانات المستخرجة
                nameField.value = extractedData.name || '';
                nationalIdField.value = extractedData.nationalId || '';
                addressField.value = extractedData.address || '';
                
                // إذا لم يتم العثور على الرقم القومي، نبحث عنه بشكل أكثر تحديداً
                if (!extractedData.nationalId) {
                    const nationalIdMatch = text.match(/\d{14}/);
                    if (nationalIdMatch) {
                        nationalIdField.value = nationalIdMatch[0];
                    }
                }
                
                showStatus('تم استخراج البيانات بنجاح!', 'success');
                
                // تفعيل إضافة البيانات للجدول
                setTimeout(() => {
                    addToExcelTable();
                }, 500);
                
            } catch (error) {
                console.error('خطأ في معالجة الصورة:', error);
                showStatus('فشل في استخراج البيانات. حاول مرة أخرى مع صورة أكثر وضوحاً.', 'error');
                progressText.textContent = 'فشل في استخراج البيانات';
            } finally {
                processImageBtn.disabled = false;
            }
        });
        
        // تحليل النص المستخرج للعثور على البيانات في البطاقة المصرية
        function parseArabicIDText(text) {
            const result = {
                name: '',
                nationalId: '',
                address: ''
            };
            
            // تنظيف النص وإزالة الرموز الغريبة
            let cleanedText = text.replace(/[^\u0600-\u06FF\u0750-\u077F\u08A0-\u08FF\uFB50-\uFDFF\uFE70-\uFEFF0-9\s.,]/g, ' ');
            cleanedText = cleanedText.replace(/\s+/g, ' ').trim();
            
            console.log("النص المستخرج بعد التنظيف:", cleanedText);
            
            // تقسيم النص إلى أسطر
            const lines = text.split('\n').map(line => line.trim()).filter(line => line.length > 0);
            
            // البحث عن الرقم القومي (14 رقم)
            const nationalIdMatch = cleanedText.match(/\d{14}/);
            if (nationalIdMatch) {
                result.nationalId = nationalIdMatch[0];
            }
            
            // البحث عن الاسم (عادة يكون في الأعلى أو البداية)
            // في البطاقة المصرية، الاسم عادة يكون في الأعلى
            if (lines.length > 0) {
                // نبحث عن سطر يحتوي على اسم عربي (أكثر من كلمتين)
                for (let i = 0; i < Math.min(lines.length, 3); i++) {
                    const line = lines[i];
                    const arabicWords = line.match(/[\u0600-\u06FF]{2,}/g);
                    if (arabicWords && arabicWords.length >= 2) {
                        result.name = line;
                        break;
                    }
                }
                
                // إذا لم نجد الاسم في الأسطر الأولى، نأخذ أول سطر طويل
                if (!result.name && lines[0].length > 5) {
                    result.name = lines[0];
                }
            }
            
            // البحث عن العنوان (عادة يكون بعد الاسم وقبل الرقم القومي)
            if (result.name && result.nationalId) {
                const nameIndex = cleanedText.indexOf(result.name);
                const idIndex = cleanedText.indexOf(result.nationalId);
                
                if (nameIndex !== -1 && idIndex !== -1 && idIndex > nameIndex) {
                    const addressText = cleanedText.substring(nameIndex + result.name.length, idIndex).trim();
                    if (addressText.length > 5) {
                        result.address = addressText;
                    }
                }
            }
            
            // إذا لم نجد العنوان بالطريقة السابقة، نبحث عنه في منتصف النص
            if (!result.address && lines.length > 2) {
                const midLines = lines.slice(1, lines.length - 1);
                if (midLines.length > 0) {
                    // نأخذ السطور التي تحتوي على كلمات عربية ولا تحتوي على أرقام طويلة
                    const addressLines = midLines.filter(line => {
                        const hasArabic = /[\u0600-\u06FF]/.test(line);
                        const hasLongNumbers = /\d{5,}/.test(line);
                        return hasArabic && !hasLongNumbers && line.length > 5;
                    });
                    
                    if (addressLines.length > 0) {
                        result.address = addressLines.join(' ');
                    }
                }
            }
            
            // تنظيف الاسم والعنوان من الأرقام الطويلة
            result.name = result.name.replace(/\d{10,}/g, '').trim();
            result.address = result.address.replace(/\d{10,}/g, '').trim();
            
            return result;
        }
        
        // إضافة البيانات للجدول
        function addToExcelTable() {
            const name = nameField.value.trim();
            const nationalId = nationalIdField.value.trim();
            const address = addressField.value.trim();
            const unitNumber = unitNumberField.value.trim();
            
            if (!name || !nationalId || !unitNumber) {
                showStatus('الرجاء التأكد من ملء الاسم والرقم القومي ورقم الوحدة.', 'error');
                return;
            }
            
            // إضافة البيانات للمصفوفة
            cardsData.push({
                name,
                nationalId,
                address,
                unitNumber,
                date: new Date().toLocaleDateString('ar-EG')
            });
            
            // تحديث الجدول
            updateDataTable();
            
            // الانتقال إلى تبويب الإكسل
            document.querySelector('[data-tab="excel-tab"]').click();
            
            showStatus('تم إضافة البيانات للجدول بنجاح!', 'success');
            
            // إعادة تعيين الحقول (باستثناء unit number)
            nameField.value = '';
            nationalIdField.value = '';
            addressField.value = '';
            unitNumberField.value = '';
            
            // إخفاء صورة البطاقة السابقة
            capturedImageContainer.style.display = 'none';
            capturedImageData = null;
            processedImageData = null;
            ocrProgress.style.display = 'none';
        }
        
        // تحديث جدول البيانات
        function updateDataTable() {
            tableBody.innerHTML = '';
            
            cardsData.forEach((card, index) => {
                const row = document.createElement('tr');
                
                row.innerHTML = `
                    <td>${card.name}</td>
                    <td>${card.nationalId}</td>
                    <td>${card.address || 'غير محدد'}</td>
                    <td>${card.unitNumber}</td>
                `;
                
                tableBody.appendChild(row);
            });
            
            // إظهار الجدول إذا كان فيه بيانات
            if (cardsData.length > 0) {
                dataTable.style.display = 'block';
            }
        }
        
        // تصدير البيانات لملف إكسل
        exportExcelBtn.addEventListener('click', () => {
            if (cardsData.length === 0) {
                showStatus('لا توجد بيانات لتصديرها. الرجاء إضافة بيانات أولاً.', 'error');
                return;
            }
            
            // تحويل البيانات لورقة عمل
            const wsData = [
                ['الاسم', 'الرقم القومي', 'العنوان', 'رقم الوحدة', 'تاريخ الإضافة'],
                ...cardsData.map(card => [
                    card.name,
                    card.nationalId,
                    card.address || '',
                    card.unitNumber,
                    card.date
                ])
            ];
            
            const ws = XLSX.utils.aoa_to_sheet(wsData);
            
            // تنسيق الأعمدة
            const wscols = [
                {wch: 25}, // الاسم
                {wch: 15}, // الرقم القومي
                {wch: 40}, // العنوان
                {wch: 15}, // رقم الوحدة
                {wch: 15}  // التاريخ
            ];
            ws['!cols'] = wscols;
            
            // إنشاء مصنف وإضافة الورقة
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, 'بطاقات الرقم القومي');
            
            // توليد اسم للملف
            const dateStr = new Date().toISOString().split('T')[0];
            const fileName = `بطاقات_الرقم_القومي_${dateStr}.xlsx`;
            
            // تنزيل الملف
            XLSX.writeFile(wb, fileName);
            
            showStatus(`تم تصدير ${cardsData.length} بطاقة إلى ملف ${fileName}`, 'success');
        });
        
        // مسح جميع البيانات
        clearDataBtn.addEventListener('click', () => {
            if (cardsData.length === 0) {
                showStatus('لا توجد بيانات لمسحها.', 'error');
                return;
            }
            
            if (confirm('هل أنت متأكد من رغبتك في مسح جميع البيانات؟ لا يمكن التراجع عن هذا الإجراء.')) {
                cardsData = [];
                updateDataTable();
                dataTable.style.display = 'none';
                showStatus('تم مسح جميع البيانات بنجاح.', 'success');
            }
        });
        
        // عرض رسالة الحالة
        function showStatus(message, type) {
            statusMessage.textContent = message;
            statusMessage.className = 'status-message ' + type;
            statusMessage.style.display = 'block';
            
            // إخفاء الرسالة بعد 5 ثواني
            setTimeout(() => {
                statusMessage.style.display = 'none';
            }, 5000);
        }
        
        // إظهار رسالة ترحيبية عند التحميل
        window.addEventListener('load', () => {
            showStatus('مرحباً! قم بتشغيل الكاميرا لبدء قراءة بطاقات الرقم القومي.', 'success');
            
            // التحقق من دعم الكاميرا
            if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                showStatus('المتصفح لا يدعم الكاميرا. يرجى استخدام متصفح حديث مثل Chrome أو Safari.', 'error');
                startCameraBtn.disabled = true;
            }
            
            // توجيه المستخدم لاستخدام الكاميرا الخلفية
            setTimeout(() => {
                showStatus('نصيحة: استخدم الكاميرا الخلفية لصور أوضح للبطاقة.', 'processing');
            }, 3000);
        });
        
        // دعم اللمس للهواتف
        let touchStartY = 0;
        let touchStartX = 0;
        
        document.addEventListener('touchstart', (e) => {
            touchStartY = e.touches[0].clientY;
            touchStartX = e.touches[0].clientX;
        }, {passive: true});
        
        document.addEventListener('touchend', (e) => {
            const touchEndY = e.changedTouches[0].clientY;
            const touchEndX = e.changedTouches[0].clientX;
            
            const diffY = touchStartY - touchEndY;
            const diffX = touchStartX - touchEndX;
            
            // إذا كانت المسافة كبيرة في الاتجاه الأفقي (سوایپ)
            if (Math.abs(diffX) > Math.abs(diffY) && Math.abs(diffX) > 50) {
                const tabsArray = Array.from(tabs);
                const currentIndex = tabsArray.findIndex(tab => tab.classList.contains('active'));
                
                if (diffX > 0 && currentIndex < tabsArray.length - 1) {
                    // سوایپ لليسار - الانتقال للتبويب التالي
                    tabsArray[currentIndex + 1].click();
                } else if (diffX < 0 && currentIndex > 0) {
                    // سوایپ لليمين - الانتقال للتبويب السابق
                    tabsArray[currentIndex - 1].click();
                }
            }
        }, {passive: true});
        
        // منع التمرير الزائد للجسم
        document.body.addEventListener('touchmove', (e) => {
            if (e.target.tagName !== 'INPUT' && e.target.tagName !== 'TEXTAREA') {
                e.preventDefault();
            }
        }, { passive: false });
        
        // اختصار لوحة المفاتيح للتقاط الصورة (مفتاح المسافة أو حجم الصوت)
        document.addEventListener('keydown', (e) => {
            if ((e.code === 'Space' || e.code === 'VolumeUp' || e.code === 'VolumeDown') && !captureBtn.disabled) {
                e.preventDefault();
                captureBtn.click();
            }
        });
        
        // تحسين الأداء على الجوال
        window.addEventListener('blur', () => {
            if (stream) {
                // إيقاف الكاميرا مؤقتاً عندما يترك المستخدم التطبيق
                stream.getTracks().forEach(track => track.enabled = false);
            }
        });
        
        window.addEventListener('focus', () => {
            if (stream) {
                // إعادة تشغيل الكاميرا عندما يعود المستخدم للتطبيق
                stream.getTracks().forEach(track => track.enabled = true);
            }
        });
    </script>
</body>
</html>
