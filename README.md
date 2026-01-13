<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
        }
        
        body {
            background: linear-gradient(135deg, #1a3a1a 0%, #2d5a2d 100%);
            color: #333;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background-color: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            overflow: hidden;
        }
        
        header {
            background: linear-gradient(to right, #c19a6b, #8b6914);
            color: white;
            padding: 25px;
            text-align: center;
            border-bottom: 5px solid #5d432c;
        }
        
        header h1 {
            font-size: 2.2rem;
            margin-bottom: 10px;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
        }
        
        header p {
            font-size: 1.1rem;
            opacity: 0.9;
        }
        
        .main-content {
            display: flex;
            flex-wrap: wrap;
            padding: 20px;
            gap: 20px;
        }
        
        .camera-section, .data-section, .excel-section {
            flex: 1;
            min-width: 300px;
            background-color: #f8f5f0;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .section-title {
            color: #5d432c;
            border-bottom: 2px solid #c19a6b;
            padding-bottom: 10px;
            margin-bottom: 20px;
            font-size: 1.5rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .section-title i {
            color: #8b6914;
        }
        
        .video-container {
            width: 100%;
            background-color: #000;
            border-radius: 10px;
            overflow: hidden;
            margin-bottom: 15px;
            position: relative;
            aspect-ratio: 4/3;
        }
        
        #video {
            width: 100%;
            height: 100%;
            object-fit: cover;
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
            gap: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        button {
            padding: 12px 25px;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        
        .btn-primary {
            background-color: #8b6914;
            color: white;
        }
        
        .btn-primary:hover {
            background-color: #6d5010;
            transform: translateY(-2px);
        }
        
        .btn-secondary {
            background-color: #5d432c;
            color: white;
        }
        
        .btn-secondary:hover {
            background-color: #47321f;
            transform: translateY(-2px);
        }
        
        .btn-success {
            background-color: #2d5a2d;
            color: white;
        }
        
        .btn-success:hover {
            background-color: #1f3f1f;
            transform: translateY(-2px);
        }
        
        .captured-image-container {
            margin-top: 20px;
            display: none;
        }
        
        #capturedImage {
            width: 100%;
            border-radius: 10px;
            border: 3px solid #c19a6b;
        }
        
        .data-form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        
        .form-group {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        
        label {
            font-weight: 600;
            color: #5d432c;
        }
        
        input, textarea {
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 1rem;
            transition: border 0.3s;
        }
        
        input:focus, textarea:focus {
            border-color: #8b6914;
            outline: none;
        }
        
        textarea {
            min-height: 100px;
            resize: vertical;
        }
        
        .ocr-progress {
            margin-top: 15px;
            display: none;
        }
        
        .progress-bar {
            width: 100%;
            height: 20px;
            background-color: #e0e0e0;
            border-radius: 10px;
            overflow: hidden;
            margin-bottom: 10px;
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
            padding: 15px;
            margin-top: 15px;
            border-left: 5px solid #2d5a2d;
        }
        
        .unit-number-section h3 {
            color: #1f3f1f;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            display: none;
        }
        
        .data-table th, .data-table td {
            border: 1px solid #c19a6b;
            padding: 10px;
            text-align: right;
        }
        
        .data-table th {
            background-color: #8b6914;
            color: white;
        }
        
        .data-table tr:nth-child(even) {
            background-color: #f5f0e6;
        }
        
        .export-controls {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 20px;
        }
        
        .instructions {
            background-color: #f0e6d6;
            border-radius: 10px;
            padding: 15px;
            margin-top: 20px;
            border-right: 5px solid #8b6914;
        }
        
        .instructions h3 {
            color: #5d432c;
            margin-bottom: 10px;
        }
        
        .instructions ol {
            padding-right: 20px;
        }
        
        .instructions li {
            margin-bottom: 8px;
            line-height: 1.5;
        }
        
        .status-message {
            padding: 12px;
            border-radius: 8px;
            margin: 15px 0;
            text-align: center;
            font-weight: 600;
            display: none;
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
        
        footer {
            text-align: center;
            padding: 20px;
            color: white;
            margin-top: 30px;
            font-size: 0.9rem;
            opacity: 0.8;
        }
        
        @media (max-width: 768px) {
            .main-content {
                flex-direction: column;
            }
            
            header h1 {
                font-size: 1.8rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-id-card"></i> قارئ بطاقة الرقم القومي المصري</h1>
            <p>اقرأ بيانات بطاقة الرقم القومي باستخدام الكاميرا واحفظها في ملف إكسل</p>
        </header>
        
        <div class="main-content">
            <!-- قسم الكاميرا -->
            <section class="camera-section">
                <h2 class="section-title"><i class="fas fa-camera"></i> الكاميرا</h2>
                <div class="video-container">
                    <video id="video" autoplay playsinline></video>
                    <div class="camera-overlay"></div>
                </div>
                
                <div class="camera-controls">
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
            <section class="data-section">
                <h2 class="section-title"><i class="fas fa-user"></i> البيانات المستخرجة</h2>
                
                <form class="data-form">
                    <div class="form-group">
                        <label for="name">الاسم بالكامل:</label>
                        <input type="text" id="name" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة">
                    </div>
                    
                    <div class="form-group">
                        <label for="nationalId">الرقم القومي:</label>
                        <input type="text" id="nationalId" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة" maxlength="14">
                    </div>
                    
                    <div class="form-group">
                        <label for="address">العنوان:</label>
                        <textarea id="address" placeholder="سيتم ملؤه تلقائياً بعد قراءة البطاقة"></textarea>
                    </div>
                    
                    <div class="unit-number-section">
                        <h3><i class="fas fa-hashtag"></i> رقم الوحدة (Unit Number)</h3>
                        <div class="form-group">
                            <label for="unitNumber">الرجاء إدخال رقم الوحدة يدوياً:</label>
                            <input type="text" id="unitNumber" placeholder="أدخل رقم الوحدة هنا" required>
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
            <section class="excel-section">
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
                
                <div class="export-controls">
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
                    </ul>
                </div>
            </section>
        </div>
    </div>
    
    <footer>
        <p>تم التطوير باستخدام HTML5 و Tesseract.js و SheetJS | برنامج قراءة بطاقة الرقم القومي المصري</p>
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
        let cardsData = [];
        
        // تشغيل الكاميرا
        startCameraBtn.addEventListener('click', async () => {
            try {
                stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'environment',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    } 
                });
                
                video.srcObject = stream;
                captureBtn.disabled = false;
                startCameraBtn.disabled = true;
                showStatus('تم تشغيل الكاميرا بنجاح. قم بتوجيهها نحو بطاقة الرقم القومي.', 'success');
            } catch (err) {
                console.error('خطأ في الوصول للكاميرا:', err);
                showStatus('فشل في الوصول للكاميرا. تأكد من السماح بالوصول إلى الكاميرا.', 'error');
            }
        });
        
        // التقاط صورة
        captureBtn.addEventListener('click', () => {
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            
            capturedImageData = canvas.toDataURL('image/jpeg');
            capturedImage.src = capturedImageData;
            capturedImageContainer.style.display = 'block';
            processImageBtn.disabled = false;
            
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
        });
        
        // معالجة الصورة واستخراج البيانات
        processImageBtn.addEventListener('click', async () => {
            if (!capturedImageData) {
                showStatus('لم يتم التقاط صورة بعد. الرجاء التقاط صورة أولاً.', 'error');
                return;
            }
            
            if (!unitNumberField.value.trim()) {
                showStatus('الرجاء إدخال رقم الوحدة (Unit Number) أولاً.', 'error');
                unitNumberField.focus();
                return;
            }
            
            // إظهار شريط التقدم
            ocrProgress.style.display = 'block';
            progressFill.style.width = '0%';
            progressText.textContent = 'جاري تحميل محرك OCR...';
            processImageBtn.disabled = true;
            
            showStatus('جاري معالجة الصورة واستخراج البيانات...', 'processing');
            
            try {
                // تحويل dataURL إلى blob
                const response = await fetch(capturedImageData);
                const blob = await response.blob();
                
                // استخدام Tesseract.js لاستخراج النص
                const worker = await Tesseract.createWorker('ara', 1, {
                    logger: (m) => {
                        if (m.status === 'recognizing text') {
                            const progress = m.progress * 100;
                            progressFill.style.width = `${progress}%`;
                            progressText.textContent = `جاري استخراج النص: ${Math.round(progress)}%`;
                        }
                    }
                });
                
                // معالجة الصورة
                const { data: { text } } = await worker.recognize(blob);
                await worker.terminate();
                
                progressFill.style.width = '100%';
                progressText.textContent = 'تم استخراج النص بنجاح!';
                
                // تحليل النص المستخرج
                const extractedData = parseExtractedText(text);
                
                // تعبئة الحقول بالبيانات المستخرجة
                nameField.value = extractedData.name || '';
                nationalIdField.value = extractedData.nationalId || '';
                addressField.value = extractedData.address || '';
                
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
        
        // تحليل النص المستخرج للعثور على البيانات
        function parseExtractedText(text) {
            const result = {
                name: '',
                nationalId: '',
                address: ''
            };
            
            // تنظيف النص
            const cleanedText = text.replace(/\n/g, ' ').replace(/\s+/g, ' ').trim();
            
            // البحث عن الرقم القومي (14 رقم)
            const nationalIdMatch = cleanedText.match(/\b\d{14}\b/);
            if (nationalIdMatch) {
                result.nationalId = nationalIdMatch[0];
            }
            
            // البحث عن الاسم (كلمات عربية، افتراضاً يكون في بداية النص)
            const nameMatch = cleanedText.match(/[\u0600-\u06FF\s]{3,}/);
            if (nameMatch) {
                // أخذ أول 3 كلمات كاسم
                const nameParts = nameMatch[0].trim().split(' ').slice(0, 3);
                result.name = nameParts.join(' ');
            }
            
            // البحث عن العنوان (افتراضياً بعد الرقم القومي أو في نهاية النص)
            // هذا جزء افتراضي وقد يحتاج إلى تحسين حسب تنسيق البطاقة الفعلي
            if (nationalIdMatch && nationalIdMatch.index) {
                const afterNationalId = cleanedText.substring(nationalIdMatch.index + 14);
                if (afterNationalId.trim().length > 10) {
                    result.address = afterNationalId.trim().substring(0, 100);
                }
            }
            
            // إذا لم يتم العثور على عنوان، نأخذ جزءاً من النص كعنوان
            if (!result.address && cleanedText.length > 50) {
                result.address = cleanedText.substring(cleanedText.length - 100).trim();
            }
            
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
            
            // إظهار الجدول
            dataTable.style.display = 'table';
            
            showStatus('تم إضافة البيانات للجدول بنجاح!', 'success');
            
            // إعادة تعيين الحقول (باستثناء unit number)
            nameField.value = '';
            nationalIdField.value = '';
            addressField.value = '';
            unitNumberField.value = '';
            
            // إخفاء صورة البطاقة السابقة
            capturedImageContainer.style.display = 'none';
            capturedImageData = null;
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
        });
    </script>
</body>
</html>
