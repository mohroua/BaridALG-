<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>مركز الامتحان - بريد الجزائر</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f9f9f9;
      margin: 0;
      padding: 0;
    }

    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 5px 10px; /* تقليل البادينغ */
      background-color: #fff;
      border-bottom: 1px solid #ccc;
    }

    .header-content {
      display: flex;
      align-items: center;
      gap: 5px; /* تقليل المسافة بين الشعار والنص */
    }

    .header-content span {
      font-size: 18px; /* تصغير الخط */
      font-weight: bold;
    }

    .header-content img {
      height: 60px; /* تصغير حجم الشعار */
    }

    .content {
      text-align: center;
      padding: 15px; /* تقليل المسافة حول المحتوى */
    }

    .box {
      background-color: #fff;
      border: 1px solid #ddd;
      border-radius: 10px;
      padding: 15px; /* تقليل الفراغ داخل المربع */
      display: inline-block;
      box-shadow: 0 0 5px rgba(0,0,0,0.1);
      width: 70%;
      max-width: 800px;
      min-width: 300px;
      margin: 0 auto;
    }

    .status {
      display: flex;
      justify-content: center;
      align-items: center;
      margin: 5px 0; /* تقليل الهامش العمودي */
    }

    .status span {
      background-color: #f0f0f0;
      border-radius: 10px;
      padding: 3px 8px; /* تقليل الحجم الداخلي */
      margin-right: 5px;
      font-size: 14px;
    }

    .status .orange {
      background-color: orange;
      color: white;
    }

    button {
      padding: 8px 16px;
      margin-top: 10px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      background-color: #ddd;
      cursor: pointer;
    }

    #exam-section {
      display: none;
      padding: 20px; /* تقليل المسافة */
    }

    .question {
      font-size: 28px; /* تقليل حجم السؤال */
      font-weight: bold;
      margin-bottom: 10px;
    }

    label {
      display: block;
      margin: 5px 0; /* تقليل الفراغ بين الخيارات */
      font-size: 15px;
    }

    .hidden {
      display: none;
    }

    #timer {
      font-weight: bold;
      color: red;
      margin: 5px 0; /* تقليل المسافة حول المؤقت */
    }

    #nav-buttons {
      margin-top: 10px;
    }

    .correct {
      color: green;
      font-weight: bold;
    }
    .wrong {
      color: red;
      font-weight: bold;
    }
    .question-block {
      margin-bottom: 20px;
    }

    /* الأنماط لرسالة الإقصاء */
    #disqualification-container {
      background-color: #ffe6e6; /* خلفية حمراء فاتحة */
      padding: 30px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      max-width: 600px;
      width: 90%;
      border: 2px solid #ff4d4d; /* حدود حمراء */
      text-align: center;
      margin: 50px auto; /* لتوسيط الرسالة */
    }

    #disqualification-text {
      color: #ff0000; /* نص أحمر للإقصاء */
      font-size: 2em;
      font-weight: bold;
      margin-bottom: 20px;
    }

    #retry-button {
      background-color: #4CAF50; /* خلفية خضراء */
      color: white;
      padding: 15px 30px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1.2em;
      transition: background-color 0.3s ease;
    }

    #retry-button:hover {
      background-color: #45a049; /* أخضر أغمق عند التحويم */
    }
  </style>

</head>

<body>

  <div class="header">
    <div class="header-content">
      <img src="https://i.postimg.cc/qv9RbZRT/images-15.jpg" alt="Logo">
      <span>مركز الامتحان</span>
    </div>
  </div>


  <div class="content" id="welcome-section">
    <h2 id="username">مرحبا المترشح</h2>
    <div class="box">
      <h3> مكلف بالزبائن</h3>
      <div class="status">
        <span>55 الأسئلة</span>
        <span class="orange">لم يبدأ بعد</span>
      </div>
      <p>امتحان مكلف بالزبائن</p>
      <button onclick="startExam()">بدء الامتحان</button>
    </div>
  </div>

  <div id="exam-section" class="hidden">
    <div class="question" id="question-text"></div>
    <div id="timer"></div>
    <div id="choices"></div>
    <div id="nav-buttons">
      <button id="prevBtn" onclick="prevQuestion()">السابق</button>
      <button onclick="nextQuestion()">التالي</button>
    </div>
    <div id="result" class="hidden"></div>
  </div>

  <div id="correction-section" class="hidden"></div>

  <script>
    // 👇 اختر الوضع هنا: "training" أو "official"
    const mode = "training"; // قم بتغيير هذا إلى "official" عند الحاجة
    let examStarted = false; // متغير لتتبع حالة بدء الامتحان

    document.addEventListener("DOMContentLoaded", function () {
        const welcomeSection = document.getElementById("welcome-section");
        const examSection = document.getElementById("exam-section");

        // دالة لعرض رسالة الإقصاء بناءً على الوضع
        function showDisqualificationMessage() {
            // إخفاء كل أقسام الصفحة
            welcomeSection.classList.add("hidden");
            examSection.classList.add("hidden");

            // مسح المحتوى الحالي لـ body بالكامل لضمان عدم ظهور أي شيء آخر
            document.body.innerHTML = '';

            const div = document.createElement('div');
            div.id = 'disqualification-container';
            div.className = 'disqualification-container';

            if (mode === "training") {
                div.innerHTML = `
                    <p id="disqualification-text">لقد تم إقصاؤك من الامتحان!</p>
                    <p>هذا اختبار تجريبي، يمكنك إعادة المحاولة.</p>
                    <button id="retry-button">إعادة المحاولة</button>
                `;
                document.body.appendChild(div);
                document.getElementById("retry-button").addEventListener("click", function () {
                    // مسح علامة الإقصاء والسماح بإعادة المحاولة
                    localStorage.removeItem("disqualified");
                    location.reload(); // إعادة تحميل الصفحة
                });
            } else if (mode === "official") {
                div.innerHTML = `
                    <p id="disqualification-text">لقد تم إقصاؤك من الامتحان!</p>
                    <p>لا يمكنك إعادة الدخول مرة أخرى.</p>
                `;
                document.body.appendChild(div);
            }
        }

        // التحقق عند تحميل الصفحة إذا كان المستخدم قد تم إقصاؤه مسبقًا
        if (localStorage.getItem("disqualified") === "true") {
            showDisqualificationMessage();
        } else {
            // إظهار المحتوى الأصلي للصفحة إذا لم يتم الإقصاء
            welcomeSection.classList.remove("hidden");
            examSection.classList.add("hidden"); // تأكد من إخفاء قسم الامتحان في البداية
        }

        // معالج الحدث عند محاولة المستخدم مغادرة الصفحة أو تحديثها
        window.addEventListener("beforeunload", function (event) {
            // إذا كان الامتحان قد بدأ ولم ينتهِ بعد (أي، لم تظهر صفحة النتائج)
            // هذا هو الشرط الذي يحدد ما إذا كان يجب تسجيل الإقصاء
            if (examStarted && document.getElementById("result").classList.contains("hidden")) {
                 localStorage.setItem("disqualified", "true");
            }
            // تم إزالة event.preventDefault() و event.returnValue = ''; لعدم إظهار الرسالة
        });

        // التقاط الاسم من الرابط
        function getNameFromURL() {
            const params = new URLSearchParams(window.location.search);
            return params.get("name") || "المترشح";
        }

        // عرض الاسم
        const name = getNameFromURL();
        document.getElementById("username").textContent = `مرحبا ${name.replace(/\./g, ' ')}`;


        // بدء الامتحان (تم جعلها دالة عامة ليمكن استدعاؤها من HTML)
        window.startExam = function() {
            examStarted = true; // تعيين حالة بدء الامتحان إلى صحيح
            welcomeSection.style.display = "none";
            examSection.style.display = "block";
            showQuestion();
        };

        const questions = [];

        function addQuestion(q, options, correct) {
            questions.push({ q, choices: options, correct });
        }

        // أسئلتك هنا



addQuestion("ما هو الفرق بين البريد الموصى عليه والبريد العادي؟", ["البريد العادي يصل أسرع", "الموصى عليه يضمن التتبع والإشعار بالاستلام", "البريد العادي يُرسل دوليًا فقط", "البريد الموصى عليه لا يتطلب توقيعًا"], 1);

addQuestion("ما هو الجهاز المستخدم لتسجيل التسليم الإلكتروني؟", ["الطابعة", "PDA", "الهاتف الثابت", "جهاز التلفاز"], 1);

addQuestion("ما الإجراء الذي يجب على الساعي اتباعه عند رفض الزبون استلام البريد؟", ["تسليم الطرد لجار الزبون", "إعادة الطرد إلى المكتب وتسجيل السبب", "تدمير الطرد", "فتح الطرد"], 1);

addQuestion("في حال وجود خطأ في العنوان، ماذا يجب على الساعي فعله؟", ["يفتح الطرد", "يسلمه لمن يجده", "يعيده إلى المكتب", "يحتفظ به"], 2);

addQuestion("ما هو EMS؟", ["نوع من الطوابع", "بريد سريع داخلي ودولي", "تحويل مالي", "تطبيق هاتفي"], 1);

addQuestion("كيف يتأكد ساعي البريد من هوية الشخص المستلم؟", ["يثق بكلامه", "يتأكد من بطاقة الهوية", "يسأل الجيران", "لا حاجة للتأكد"], 1);

addQuestion("ما معنى RIP؟", ["حساب توفير", "رقم التعريف البريدي", "رقم الشيك البريدي", "رقم التحويل الدولي"], 1);

addQuestion("ما هي خدمة Mandat Express؟", ["إرسال بريد", "إرسال أموال بشكل عاجل", "شراء طوابع", "سحب النقود من CCP"], 1);

addQuestion("كيف يتم تتبع الطرود البريدية؟", ["عن طريق الإنترنت باستخدام رقم التتبع", "بالسؤال في المكتب", "عن طريق الهاتف فقط", "لا يمكن تتبعها"], 0);

addQuestion("من الجهة التي تشرف على بريد الجزائر؟", ["وزارة المالية", "وزارة النقل", "وزارة البريد والاتصالات", "وزارة الداخلية"], 2);

addQuestion("ما هو التصرف الصحيح إذا وجد الساعي طردًا مشبوهًا؟", ["يفتحه", "يتجاهله", "يبلغ الإدارة ويوقف التوزيع", "يسلمه فورًا"], 2);

addQuestion("أي من المهام التالية ليست من اختصاص ساعي البريد؟", ["توزيع الرسائل", "تسليم الإشعارات القضائية", "صيانة الأجهزة", "تسليم الطرود"], 2);

addQuestion("كيف يتم حساب تكلفة إرسال طرد؟", ["حسب الوزن فقط", "حسب الوجهة فقط", "حسب الوزن، الوجهة ونوع الخدمة", "ثابتة"], 2);

addQuestion("ما هو PIN PAD؟", ["جهاز إدخال رمز سري وتوقيع إلكتروني", "لوحة مفاتيح", "ماسح ضوئي", "طابعة"], 0);

addQuestion("كم يبلغ الحد الأقصى لوزن الطرد في بريد الجزائر؟", ["5 كغ", "10 كغ", "20 كغ", "30 كغ"], 3);

addQuestion("كيف يثبت الزبون استلام الطرد؟", ["توقيع يدوي أو إلكتروني", "رقم سري", "بطاقة اشتراك", "لا يثبت"], 0);

addQuestion("ما هو الإجراء عند ضياع طرد مسجل؟", ["لا شيء", "يُفتح تحقيق", "يُرسل بريد آخر", "يُخصم من راتب الساعي"], 1);

addQuestion("هل يمكن لساعي البريد تسليم البريد لشخص غير المرسل إليه؟", ["نعم دائمًا", "لا أبدًا", "فقط بتفويض رسمي أو لأحد أفراد العائلة", "فقط بإذن شفوي"], 2);

addQuestion("ما الفرق بين الطابع العادي والطابع المستعجل؟", ["الطابع العادي أغلى", "الطابع المستعجل يسرّع التوصيل", "لا فرق", "المستعجل يُستخدم دوليًا فقط"], 1);

addQuestion("ما معنى عبارة 'إشعار بالاستلام'؟", ["وثيقة للزينة", "إثبات أن الزبون استلم", "رقم تتبع", "نوع من الطوابع"], 1);

addQuestion("في حال حدوث حادث أثناء التوصيل، ماذا يفعل الساعي؟", ["يواصل عمله", "يعود للبيت", "يُبلغ الإدارة فورًا ويملأ تقريرًا", "يتصل بالزبون"], 2);

addQuestion("كيف يضمن الساعي خصوصية الزبائن؟", ["بحفظ الرسائل", "بعدم فتح أو الاطلاع على المحتوى", "بعدم الرد على أسئلة الجيران", "جميع ما سبق"], 3);

addQuestion("ما هي عقوبة التلاعب بالبريد؟", ["تحذير شفوي", "خصم في الراتب", "عقوبة تأديبية وقانونية", "نقل إلى مكتب آخر"], 2);

addQuestion("من المسؤول عن تنظيم مهام ساعي البريد؟", ["الدولة", "البريد الدولي", "مدير المكتب", "مجلس الشعب"], 2);

addQuestion("هل يمكن لساعي البريد استخدام هاتفه الشخصي أثناء العمل؟", ["نعم", "لا", "فقط في حالات الضرورة", "حسب رغبة الزبائن"], 2);

addQuestion("متى يُعتبر الساعي قد أتم مهامه اليومية؟", ["عند تسليم كل الطرود", "عند انتهاء الوقت", "حسب تعليمات المكتب", "عندما يشعر بالتعب"], 0);

addQuestion("أي من القيم التالية يجب أن يتحلى بها ساعي البريد؟", ["السرية", "الأمانة", "احترام المواعيد", "جميع ما سبق"], 3);

addQuestion("ما الفرق بين خدمة Mandat Ordinaire وMandat Express؟", ["Express أسرع", "Ordinaire إلكترونية", "Express أرخص", "لا فرق"], 0);

addQuestion("ما الذي يثبت إرسال البريد؟", ["توقيع الزبون", "بطاقة الهوية", "وصل الإرسال", "لا شيء"], 2);

addQuestion("هل يمكن للساعي تسليم البريد أيام العطل؟", ["نعم دائمًا", "لا أبدًا", "فقط بإذن خاص", "في المناسبات فقط"], 2);

addQuestion("ما هو البريد غير القابل للتتبع؟", ["البريد العادي", "المضمون", "السريع", "المسجل"], 0);

addQuestion("أي من هذه التصرفات يعتبر إخلالًا بالواجب؟", ["تأخير التسليم دون مبرر", "اطلاع الغير على محتوى الطرد", "تسليم الطرد دون إثبات", "جميع ما سبق"], 3);

addQuestion("ماذا يعني إذا كان الطرد عليه عبارة 'قابل للكسر'؟", ["لا يُرسل", "يُعطى للساعي المتمرس فقط", "يُعامل بحذر خاص", "يُفتح قبل الإرسال"], 2);

addQuestion("في حال إرسال الطرد إلى عنوان خاطئ، ماذا يجب فعله؟", ["الانتظار", "إعادة الإرسال فورًا", "فتح الطرد", "تبليغ الإدارة واتباع الإجراءات"], 3);

addQuestion("ما هو التصرف في حال استلام ظرف فارغ من الزبون؟", ["تجاهله", "فتحه", "تسجيل ملاحظة في المحضر", "حرقه"], 2);
    


let currentQuestion = 0;
        let answers = Array(questions.length).fill(null);
        let timers = Array(questions.length).fill(120); // 120 ثانية
        let timerInterval;

        function showQuestion() {
            clearInterval(timerInterval);

            const q = questions[currentQuestion];
            document.getElementById("question-text").textContent = `السؤال ${currentQuestion + 1}: ${q.q}`;
            const choicesDiv = document.getElementById("choices");
            choicesDiv.innerHTML = "";

            q.choices.forEach((choice, index) => {
                const label = document.createElement("label");
                const input = document.createElement("input");
                input.type = "radio";
                input.name = "choice";
                input.value = index;
                // تعطيل الخيارات إذا انتهى الوقت أو تم اختيار إجابة
                input.disabled = (timers[currentQuestion] <= 0 || answers[currentQuestion] !== null);
                if (answers[currentQuestion] === index) input.checked = true;

                input.addEventListener("change", () => {
                    if (answers[currentQuestion] === null) { // السماح بالاختيار فقط إذا لم تتم الإجابة بالفعل
                        answers[currentQuestion] = index;
                        disableChoices(); // تعطيل الخيارات بعد الاختيار
                    }
                });

                label.appendChild(input);
                label.appendChild(document.createTextNode(" " + choice));
                choicesDiv.appendChild(label);
            });

            document.getElementById("prevBtn").style.display = currentQuestion === 0 ? "none" : "inline";

            startTimer();
        }

        function startTimer() {
            const timerElement = document.getElementById("timer");

            if (timers[currentQuestion] <= 0) {
                timerElement.textContent = "انتهى الوقت لهذا السؤال.";
                disableChoices(); // التأكد من تعطيل الخيارات إذا انتهى الوقت
                return;
            }

            updateTimerText();

            timerInterval = setInterval(() => {
                timers[currentQuestion]--;
                updateTimerText();

                if (timers[currentQuestion] <= 0) {
                    clearInterval(timerInterval);
                    timerElement.textContent = "انتهى الوقت لهذا السؤال.";
                    disableChoices();
                    // لا تنتقل تلقائيا للسؤال التالي في هذا السيناريو، اترك المستخدم ينتقل بنفسه أو ينهي الامتحان
                    // setTimeout(nextQuestion, 1000); 
                }
            }, 1000);
        }

        function updateTimerText() {
            const timerElement = document.getElementById("timer");
            const seconds = timers[currentQuestion];
            const minutes = Math.floor(seconds / 60);
            const remainingSeconds = seconds % 60;

            const minStr = minutes.toString().padStart(2, '0');
            const secStr = remainingSeconds.toString().padStart(2, '0');

            timerElement.textContent = `الوقت المتبقي: ${minStr}:${secStr}`;
        }

        function disableChoices() {
            const inputs = document.querySelectorAll("input[name='choice']");
            inputs.forEach(input => input.disabled = true);
        }

        // جعل الدالة عامة ليمكن استدعاؤها من HTML
        window.nextQuestion = function() {
            if (currentQuestion < questions.length - 1) {
                currentQuestion++;
                showQuestion();
            } else {
                finishQuiz();
            }
        };

        // جعل الدالة عامة ليمكن استدعاؤها من HTML
        window.prevQuestion = function() {
            if (currentQuestion > 0) {
                currentQuestion--;
                showQuestion();
            }
        };

        function finishQuiz() {
            clearInterval(timerInterval);
            examStarted = false; // الامتحان انتهى بنجاح، لذا لن يتم تسجيل إقصاء
            localStorage.removeItem("disqualified"); // مسح حالة الإقصاء عند الانتهاء بنجاح

            examSection.classList.add("hidden"); // إخفاء قسم الامتحان
            document.getElementById("question-text").classList.add("hidden");
            document.getElementById("choices").classList.add("hidden");
            document.getElementById("timer").classList.add("hidden");
            document.getElementById("nav-buttons").classList.add("hidden");

            let correct = 0;
            let wrong = 0;

            questions.forEach((q, i) => {
                // التحقق مما إذا تم الإجابة على السؤال
                if (answers[i] !== null) {
                    if (answers[i] === q.correct) {
                        correct++;
                    } else {
                        wrong++;
                    }
                }
            });

            let finalScore = correct - wrong;
            if (finalScore < 0) finalScore = 0; // لا يمكن أن تكون النتيجة سلبية

            const resultDiv = document.getElementById("result");
            resultDiv.classList.remove("hidden");

            let message = "";
            if (finalScore >= Math.ceil(questions.length / 2)) {
                message = `<span style="color: green;">✔️ مبروك! لقد نجحت</span>`;
            } else {
                message = `<span style="color: red;">❌ للأسف، حظ موفق في المرة القادمة</span>`;
            }

            resultDiv.innerHTML = `
                <h2>انتهى الاختبار</h2>
                <p>عدد الإجابات الصحيحة: ${correct}</p>
                <p>عدد الإجابات الخاطئة: ${wrong}</p>
                <p><strong>علامتك النهائية: ${finalScore} من ${questions.length}</strong></p>
                <p>${message}</p>
                <footer style="
                    position: fixed;
                    bottom: 0;
                    width: 85%;
                    text-align: center;
                    font-size: 12px;
                    color: #555;
                    background-color: transparent;
                    padding: 5px 0;
                    direction: rtl;
                ">
                    Created by: Haricha Younes
                </footer>
                <button onclick="showCorrectionPage()">تصحيح الاختبار</button>
            `;
        }

        // جعل الدالة عامة ليمكن استدعاؤها من HTML
        window.showCorrectionPage = function() {
            document.getElementById("result").classList.add("hidden");
            const correctionSection = document.getElementById("correction-section");
            correctionSection.innerHTML = "<h2>تصحيح الاختبار:</h2>";

            questions.forEach((q, index) => {
                const userAnswer = answers[index];
                const correctAnswer = q.correct;

                const div = document.createElement("div");
                div.style.padding = "10px";
                div.style.marginBottom = "10px";
                div.style.borderRadius = "10px";
                div.style.backgroundColor = userAnswer === correctAnswer ? "#d4edda" : "#f8d7da"; // أخضر للفصل، أحمر للخطأ

                div.innerHTML = `
                    <p><strong>سؤال ${index + 1}:</strong> ${q.q}</p>
                    <p>✔️ الإجابة الصحيحة: ${q.choices[correctAnswer]}</p>
                    <p>📝 إجابتك: ${userAnswer !== null ? q.choices[userAnswer] : "لم يجب"}</p>
                `;

                correctionSection.appendChild(div);
            });

            correctionSection.classList.remove("hidden");
        };
    });
  </script>
</body>
</html>
