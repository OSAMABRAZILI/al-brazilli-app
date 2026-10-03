// ═══════════════════════════════════════════════════════════
// 🏛️ Al Brazili Technologies - Cloud Functions
// ═══════════════════════════════════════════════════════════

const functions = require('firebase-functions');
const admin = require('firebase-admin');
const axios = require('axios');

admin.initializeApp();
const db = admin.firestore();

// ═══════════════════════════════════════════════════════════
// 📚 ثوابت الخطوات
// ═══════════════════════════════════════════════════════════

const BUILDING_STEPS = [
  { num: 1, name: 'استلام المستندات', dept: 'الحي', duration: 1 },
  { num: 2, name: 'الرفع المساحي', dept: 'المساحة', duration: 4 },
  { num: 3, name: 'التقديم على بيان صلاحية موقع', dept: 'الحي - التنظيم', duration: 1 },
  { num: 4, name: 'تأشيرة مدير التنظيم', dept: 'مدير التنظيم', duration: 1 },
  { num: 5, name: 'إرسال الملف إلى مدينة الجيزة', dept: 'مدينة الجيزة', duration: 1 },
  { num: 6, name: 'رسوم التصوير', dept: 'المالية', duration: 1 },
  { num: 7, name: 'تأشيرة التخطيط', dept: 'إدارة التخطيط', duration: 2 },
  { num: 8, name: 'تأشيرة الأملاك', dept: 'إدارة الأملاك', duration: 2 },
  { num: 9, name: 'تأشيرة التقسيم', dept: 'التقسيم', duration: 2 },
  { num: 10, name: 'تأشيرة التحسين', dept: 'إدارة التحسين', duration: 2 },
  { num: 11, name: 'كتابة بيان الصلاحية', dept: 'مدينة الجيزة', duration: 1 },
  { num: 12, name: 'إصدار بيان الصلاحية', dept: 'الحي', duration: 2 },
  { num: 13, name: 'وثيقة التأمين', dept: 'المجمعة العشرية', duration: 1 },
  { num: 14, name: 'تقديم طلب رخصة البناء', dept: 'الحي', duration: 15 },
  { num: 15, name: 'إصدار رخصة البناء', dept: 'الحي', duration: 1 }
];

const ELEVATION_STEPS = [
  { num: 1, name: 'استلام المستندات', dept: 'الحي', duration: 1 },
  { num: 2, name: 'الرفع المساحي', dept: 'المساحة', duration: 4 },
  { num: 3, name: 'التقديم على بيان صلاحية موقع', dept: 'الحي - التنظيم', duration: 1 },
  { num: 4, name: 'تأشيرة مدير التنظيم', dept: 'مدير التنظيم', duration: 1 },
  { num: 5, name: 'إرسال الملف إلى مدينة الجيزة', dept: 'مدينة الجيزة', duration: 1 },
  { num: 6, name: 'رسوم التصوير', dept: 'المالية', duration: 1 },
  { num: 7, name: 'تأشيرة التخطيط', dept: 'إدارة التخطيط', duration: 2 },
  { num: 8, name: 'تأشيرة الأملاك', dept: 'إدارة الأملاك', duration: 2 },
  { num: 9, name: 'تأشيرة التقسيم', dept: 'التقسيم', duration: 2 },
  { num: 10, name: 'تأشيرة التحسين', dept: 'إدارة التحسين', duration: 2 },
  { num: 11, name: 'كتابة بيان الصلاحية', dept: 'مدينة الجيزة', duration: 1 },
  { num: 12, name: 'إصدار بيان الصلاحية', dept: 'الحي', duration: 2 },
  { num: 13, name: 'وثيقة التأمين', dept: 'المجمعة العشرية', duration: 1 },
  { num: 14, name: 'تقديم طلب رخصة التعلية', dept: 'الحي', duration: 15 },
  { num: 15, name: 'إصدار رخصة التعلية', dept: 'الحي', duration: 1 }
];

const RECONCILIATION_STEPS = [
  { num: 1, name: 'تجهيز المستندات الخاصة بالتصالح', dept: 'المكتب', duration: 2 },
  { num: 2, name: 'تقديم المستندات إلى الحي', dept: 'الحي', duration: 1 },
  { num: 3, name: 'تحديد رسوم جدية التصالح', dept: 'الحي', duration: 2 },
  { num: 4, name: 'تحديد رسوم معاينة الهيئة الهندسية', dept: 'الهيئة الهندسية', duration: 2 },
  { num: 5, name: 'سداد رسوم جدية التصالح + رسوم المعاينة', dept: 'المالية', duration: 1 },
  { num: 6, name: 'انتقال الملف إلى بيانات العقار', dept: 'بيانات العقار', duration: 3 },
  { num: 7, name: 'انتقال الملف إلى جهة الولاية', dept: 'جهة الولاية', duration: 3 },
  { num: 8, name: 'اللجنة الفنية وإصدار نموذج 6', dept: 'اللجنة الفنية', duration: 7 },
  { num: 9, name: 'سداد رسوم التصالح', dept: 'المالية', duration: 1 },
  { num: 10, name: 'إصدار نموذج 7 مؤقت', dept: 'الحي', duration: 2 },
  { num: 11, name: 'تحويل الملف إلى الهيئة الهندسية', dept: 'الهيئة الهندسية', duration: 3 },
  { num: 12, name: 'تواصل الهيئة مع صاحب التصالح', dept: 'الهيئة الهندسية', duration: 5 },
  { num: 13, name: 'إصدار المطابقة الأولى', dept: 'الهيئة الهندسية', duration: 3 },
  { num: 14, name: 'إصدار المطابقة الثانية', dept: 'الهيئة الهندسية', duration: 3 },
  { num: 15, name: 'سداد الرسوم الإضافية', dept: 'المالية', duration: 1 },
  { num: 16, name: 'إصدار نموذج 8 النهائي', dept: 'الحي', duration: 2 }
];

// ═══════════════════════════════════════════════════════════
// 🔧 دوال مساعدة
// ═══════════════════════════════════════════════════════════

function getStepsForType(collName) {
  if (collName === 'buildingPermits') return BUILDING_STEPS;
  if (collName === 'elevationPermits') return ELEVATION_STEPS;
  if (collName === 'reconciliations') return RECONCILIATION_STEPS;
  return [];
}

function getTypeLabel(collName) {
  if (collName === 'buildingPermits') return 'رخصة بناء';
  if (collName === 'elevationPermits') return 'رخصة تعلية';
  if (collName === 'reconciliations') return 'طلب تصالح';
  return '';
}

function getTypeEmoji(collName) {
  if (collName === 'buildingPermits') return '🏗️';
  if (collName === 'elevationPermits') return '📐';
  if (collName === 'reconciliations') return '⚖️';
  return '📋';
}

function getDayNameAr(date) {
  const days = ['الأحد', 'الإثنين', 'الثلاثاء', 'الأربعاء', 'الخميس', 'الجمعة', 'السبت'];
  return days[date.getDay()];
}

function getMonthNameAr(month) {
  const months = ['يناير', 'فبراير', 'مارس', 'أبريل', 'مايو', 'يونيو',
                  'يوليو', 'أغسطس', 'سبتمبر', 'أكتوبر', 'نوفمبر', 'ديسمبر'];
  return months[month];
}

function formatArabicDate(date) {
  return `${getDayNameAr(date)}، ${date.getDate()} ${getMonthNameAr(date.getMonth())} ${date.getFullYear()}`;
}

function formatArabicTime(date) {
  let hours = date.getHours();
  const minutes = String(date.getMinutes()).padStart(2, '0');
  const period = hours >= 12 ? 'مساءً' : 'صباحاً';
  if (hours > 12) hours -= 12;
  if (hours === 0) hours = 12;
  return `${hours}:${minutes} ${period}`;
}

function cleanPhone(phone) {
  let p = String(phone || '').replace(/\D/g, '');
  if (p.startsWith('0')) p = '20' + p.substring(1);
  if (!p.startsWith('20') && p.length === 10) p = '20' + p;
  return p;
}

// ═══════════════════════════════════════════════════════════
// 🎨 بناء الرسالة المطورة
// ═══════════════════════════════════════════════════════════

function buildDailyReminderMessage(project, stepNum, collName) {
  const steps = getStepsForType(collName);
  const step = steps[stepNum - 1];
  const total = steps.length;
  const progress = ((stepNum - 1) / total) * 100;
  const now = new Date();
  const typeEmoji = getTypeEmoji(collName);
  const typeLabel = getTypeLabel(collName);
  
  // شريط تقدم نصي
  const filled = Math.round(progress / 100 * 20);
  const empty = 20 - filled;
  const progressBar = '█'.repeat(filled) + '░'.repeat(empty);
  
  const line = '━━━━━━━━━━━━━━━━━━━━━━━━━━━';
  const lineDouble = '═══════════════════════════════';
  
  return `${lineDouble}
   🏛️ *AL BRAZILI TECHNOLOGIES*
   ✨ *نظام إدارة الأعمال* ✨
${lineDouble}

📅 ${formatArabicDate(now)}
🕐 ${formatArabicTime(now)}

${line}
   🔔 *تذكير بمهمة يومية*
${line}

${typeEmoji} *نوع المعاملة:* ${typeLabel}
👤 *العميل:* ${project.client}
🔢 *رقم المعاملة:* ${project.transaction || 'لم يُحدد بعد'}
📍 *العنوان:* ${project.address || 'غير محدد'}
${project.phone ? `📱 *الجوال:* ${project.phone}` : ''}

${line}
   📌 *المطلوب اليوم* 📌
${line}

▶️ *الخطوة ${stepNum}: ${step.name}*
🏢 *الجهة:* ${step.dept}
⏱️ *المدة المتوقعة:* ${step.duration} ${step.duration > 1 ? 'أيام' : 'يوم'}
📊 *التقدم:* ${progress.toFixed(0)}% (${stepNum - 1} من ${total})

\`${progressBar}\`

${line}
   💬 *للرد السريع* 💬
${line}

اضغط على الرقم المناسب أو اكتب ردك:

*1* ✅ تم الإنجاز
*2* ⏸️ تأجيل المهمة
*3* ⚠️ يوجد مشكلة
*4* ❓ استفسار
*5* 📊 تحديث الحالة

🤖 *الرد التلقائي سيحدث المعاملة فوراً*

${line}

⚠️ *ملاحظة:* يتم إرسال هذه الرسالة
يومياً الساعة 9 صباحاً حتى إنجاز المهمة

📞 *للطوارئ:* 01110798913

${lineDouble}
   🏛️ *Al Brazili Technologies*
   🇪🇬 جمهورية مصر العربية
${lineDouble}`;
}

// ═══════════════════════════════════════════════════════════
// 📤 دالة إرسال WhatsApp
// ═══════════════════════════════════════════════════════════

async function sendWhatsApp(phone, message) {
  const cleanNumber = cleanPhone(phone);
  
  // 🔹 Meta WhatsApp Cloud API
  const response = await axios.post(
    `https://graph.facebook.com/v18.0/${process.env.WHATSAPP_PHONE_ID}/messages`,
    {
      messaging_product: 'whatsapp',
      recipient_type: 'individual',
      to: cleanNumber,
      type: 'text',
      text: { 
        preview_url: false,
        body: message 
      }
    },
    {
      headers: {
        'Authorization': `Bearer ${process.env.WHATSAPP_TOKEN}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  return response.data;
}

// ═══════════════════════════════════════════════════════════
// ⏰ الدالة الرئيسية - ترسل يومياً 9 صباحاً
// ═══════════════════════════════════════════════════════════

exports.sendDailyReminders = functions
  .runWith({ timeoutSeconds: 540, memory: '512MB' })
  .pubsub
  .schedule('0 9 * * *')
  .timeZone('Africa/Cairo')
  .onRun(async (context) => {
    
    console.log('🚀 بدء إرسال التذكيرات اليومية...');
    const today = new Date().toISOString().split('T')[0];
    
    const collections = ['buildingPermits', 'elevationPermits', 'reconciliations'];
    let totalSent = 0;
    let totalErrors = 0;
    
    for (const collName of collections) {
      // جلب المشاريع النشطة
      const snapshot = await db.collection(collName)
        .where('status', 'in', ['under_review', 'approved', 'draft'])
        .get();
      
      console.log(`📁 ${collName}: ${snapshot.size} مشروع`);
      
      for (const docSnap of snapshot.docs) {
        const project = docSnap.data();
        const stepNum = (project.current_step || 0) + 1;
        const steps = getStepsForType(collName);
        
        // تخطي لو المشروع مكتمل
        if (stepNum > steps.length) continue;
        
        // تخطي لو ما فيش مهندس
        if (!project.engineer_id) {
          console.log(`⚠️ مشروع ${docSnap.id} بدون مهندس`);
          continue;
        }
        
        try {
          // جلب المهندس
          const engDoc = await db.collection('engineers').doc(project.engineer_id).get();
          if (!engDoc.exists) continue;
          const engineer = engDoc.data();
          
          if (!engineer.phone) continue;
          
          // منع الإرسال المكرر في نفس اليوم
          const alreadySent = await db.collection('daily_reminders')
            .where('projectId', '==', docSnap.id)
            .where('sentDate', '==', today)
            .limit(1)
            .get();
          
          if (!alreadySent.empty) {
            console.log(`⏭️ تم الإرسال مسبقاً اليوم: ${project.client}`);
            continue;
          }
          
          // بناء الرسالة
          const message = buildDailyReminderMessage(project, stepNum, collName);
          
          // إرسال WhatsApp
          await sendWhatsApp(engineer.phone, message);
          
          // تسجيل الإرسال
          await db.collection('daily_reminders').add({
            projectId: docSnap.id,
            projectType: collName,
            projectName: project.client,
            engineerId: project.engineer_id,
            engineerName: engineer.name,
            engineerPhone: cleanPhone(engineer.phone),
            stepNumber: stepNum,
            stepName: steps[stepNum - 1].name,
            message: message,
            sentDate: today,
            sentAt: admin.firestore.FieldValue.serverTimestamp(),
            status: 'sent',
            replied: false,
            replyAnalysis: null
          });
          
          totalSent++;
          console.log(`✅ تم الإرسال: ${engineer.name} ← ${project.client}`);
          
          // تأخير 1 ثانية بين الرسائل لتجنب Rate Limit
          await new Promise(r => setTimeout(r, 1000));
          
        } catch (error) {
          totalErrors++;
          console.error(`❌ خطأ في مشروع ${docSnap.id}:`, error.message);
          
          // تسجيل الخطأ
          await db.collection('reminder_errors').add({
            projectId: docSnap.id,
            projectType: collName,
            error: error.message,
            sentDate: today,
            createdAt: admin.firestore.FieldValue.serverTimestamp()
          });
        }
      }
    }
    
    console.log(`\n📊 الملخص النهائي:`);
    console.log(`   ✅ تم الإرسال: ${totalSent}`);
    console.log(`   ❌ فشل: ${totalErrors}`);
    
    // تسجيل ملخص العملية
    await db.collection('reminder_logs').add({
      date: today,
      sent: totalSent,
      errors: totalErrors,
      runAt: admin.firestore.FieldValue.serverTimestamp()
    });
    
    return { sent: totalSent, errors: totalErrors };
  });

// ═══════════════════════════════════════════════════════════
// 📨 Webhook لاستقبال ردود WhatsApp
// ═══════════════════════════════════════════════════════════

exports.whatsappWebhook = functions.https.onRequest(async (req, res) => {
  
  // ✅ التحقق من Webhook
  if (req.method === 'GET') {
    const mode = req.query['hub.mode'];
    const token = req.query['hub.verify_token'];
    const challenge = req.query['hub.challenge'];
    
    if (mode === 'subscribe' && token === process.env.WEBHOOK_VERIFY_TOKEN) {
      console.log('✅ Webhook verified');
      return res.status(200).send(challenge);
    }
    return res.sendStatus(403);
  }
  
  // ✅ استقبال الرسالة
  if (req.method === 'POST') {
    // الرد السريع لـ Meta
    res.sendStatus(200);
    
    try {
      const body = req.body;
      
      if (body.object !== 'whatsapp_business_account') return;
      
      const entry = body.entry?.[0];
      const changes = entry?.changes?.[0];
      const value = changes?.value;
      const message = value?.messages?.[0];
      
      if (!message) return;
      
      const fromPhone = message.from;
      let text = '';
      
      if (message.type === 'text') {
        text = message.text.body.trim();
      } else if (message.type === 'button') {
        text = message.button.text;
      } else if (message.type === 'interactive') {
        text = message.interactive?.button_reply?.id || 
               message.interactive?.list_reply?.id || '';
      }
      
      console.log(`📩 رسالة من ${fromPhone}: "${text}"`);
      
      // 🔍 البحث عن آخر تذكير معلق
      const remindersSnapshot = await db.collection('daily_reminders')
        .where('engineerPhone', '==', fromPhone)
        .where('replied', '==', false)
        .orderBy('sentAt', 'desc')
        .limit(1)
        .get();
      
      if (remindersSnapshot.empty) {
        console.log('⚠️ لا يوجد تذكير معلق');
        await sendWhatsApp(fromPhone, 
          `⚠️ *لا توجد مهمة معلقة*\n\nلم يتم العثور على مهمة نشطة مرتبطة برقمك.\n\n📞 للاستفسار: 01110798913`
        );
        return;
      }
      
      const reminderDoc = remindersSnapshot.docs[0];
      const reminder = reminderDoc.data();
      
      // 🧠 تحليل الرد
      const analysis = analyzeReply(text);
      
      // ✅ حفظ الرد
      await reminderDoc.ref.update({
        replied: true,
        replyText: text,
        replyAt: admin.firestore.FieldValue.serverTimestamp(),
        replyAnalysis: analysis
      });
      
      // 🎯 تنفيذ الإجراء
      const result = await processReply(reminder, text, analysis);
      
      // 📤 إرسال تأكيد للمهندس
      if (result.confirmation) {
        await sendWhatsApp(fromPhone, result.confirmation);
      }
      
    } catch (error) {
      console.error('❌ خطأ Webhook:', error);
    }
    
    return;
  }
  
  res.sendStatus(405);
});

// ═══════════════════════════════════════════════════════════
// 🧠 تحليل الردود بالعربي
// ═══════════════════════════════════════════════════════════

function analyzeReply(text) {
  const t = text.toLowerCase().trim();
  
  // ✅ تم الإنجاز (يدعم الأرقام والكلمات)
  if (/^(1|تم|خلص|خلصت|انتهى|انتهت|انجز|انجزت|اكتمل|اكتملت|تم بنجاح|تم الانجاز|تم الانتهاء|تمام|اوك|✅|👍|✓|✔️)$/i.test(t)) {
    return { action: 'complete', confidence: 0.98, label: 'تم الإنجاز' };
  }
  
  // ⏸️ تأجيل
  if (/^(2|تأجيل|اجل|أجل|مش دلوقتي|مش دلوقت|بكرة|بكره|مشغول|هتأجل|تأجل|⏸️|⏳)$/i.test(t)) {
    return { action: 'delay', confidence: 0.95, label: 'تأجيل' };
  }
  
  // ⚠️ مشكلة
  if (/^(3|مشكلة|مشكله|عائق|مفيش|مش موجود|رفض|مرفوض|⚠️|❗|❌)/i.test(t)) {
    const details = t.replace(/^(3|مشكلة|مشكله|عائق|⚠️|❗|❌)\s*/i, '').trim();
    return { 
      action: 'problem', 
      confidence: 0.9, 
      label: 'مشكلة',
      details: details || text
    };
  }
  
  // ❓ استفسار
  if (/^(4|استفسار|سؤال|ايه|ازاي|امتى|امتي|ليه|متى|فين|❓|🤔)/i.test(t)) {
    const details = t.replace(/^(4|استفسار|سؤال|❓|🤔)\s*/i, '').trim();
    return {
      action: 'inquiry',
      confidence: 0.85,
      label: 'استفسار',
      details: details || text
    };
  }
  
  // 📊 تحديث
  if (/^(5|تحديث|فيه جديد|وصل|وصلت|تم التقديم|اتقدم|قدمت|📊|📈)/i.test(t)) {
    const details = t.replace(/^(5|تحديث|📊|📈)\s*/i, '').trim();
    return {
      action: 'update',
      confidence: 0.8,
      label: 'تحديث',
      details: details || text
    };
  }
  
  // 🔍 تحليل نصي أذكى (بدون أرقام)
  if (/تم|خلص|انتهى|انجز|اكتمل|✅|👍/i.test(t)) {
    return { action: 'complete', confidence: 0.7, label: 'تم الإنجاز' };
  }
  if (/تأجيل|أجل|مشغول|بكرة|⏸️/i.test(t)) {
    return { action: 'delay', confidence: 0.7, label: 'تأجيل' };
  }
  if (/مشكلة|عائق|رفض|⚠️/i.test(t)) {
    return { action: 'problem', confidence: 0.7, label: 'مشكلة', details: text };
  }
  
  return { action: 'unknown', confidence: 0.3, label: 'غير معروف', raw: text };
}

// ═══════════════════════════════════════════════════════════
// 🎯 تنفيذ الإجراء حسب الرد
// ═══════════════════════════════════════════════════════════

async function processReply(reminder, text, analysis) {
  const { projectId, projectType, stepNumber, engineerId, projectName, stepName } = reminder;
  
  const projectRef = db.collection(projectType).doc(projectId);
  const projectDoc = await projectRef.get();
  if (!projectDoc.exists) {
    return { confirmation: '⚠️ المشروع غير موجود' };
  }
  
  const project = projectDoc.data();
  const steps = getStepsForType(projectType);
  const totalSteps = steps.length;
  const now = new Date();
  
  const line = '━━━━━━━━━━━━━━━━━━━━━━━━━━━';
  
  switch (analysis.action) {
    
    // ✅ تم الإنجاز
    case 'complete':
      const nextStep = stepNumber + 1;
      const isLast = stepNumber >= totalSteps;
      
      await projectRef.update({
        current_step: stepNumber,
        status: isLast ? 'completed' : 'under_review',
        lastAutoUpdate: admin.firestore.FieldValue.serverTimestamp(),
        updatedBy: 'auto_reply',
        updatedByName: 'رد المهندس التلقائي'
      });
      
      // تسجيل تنبيه
      await db.collection('alerts').add({
        type: projectType,
        projectId: projectId,
        message: `✅ تم إنجاز "${stepName}" تلقائياً - ${projectName}`,
        cat: 'status',
        date: now.toLocaleString('ar-EG'),
        createdAt: admin.firestore.FieldValue.serverTimestamp(),
        automated: true,
        replyAction: 'complete'
      });
      
      // إشعار المدير
      await notifyManager(
        `✅ *تم إنجاز خطوة*\n\n` +
        `👤 العميل: ${projectName}\n` +
        `📋 الخطوة: ${stepName}\n` +
        `👷 المهندس: ${reminder.engineerName}\n` +
        `📊 التقدم: ${stepNumber}/${totalSteps}`
      );
      
      if (isLast) {
        return {
          confirmation: `${line}
   🎉 *تهانينا* 🎉
${line}

✅ *تم إنجاز المشروع بالكامل!*

👤 *العميل:* ${projectName}
📋 *إجمالي الخطوات:* ${totalSteps}
🎯 *الحالة:* مكتمل

${line}

📞 سيتم التواصل معك قريباً
🏛️ *Al Brazili Technologies*
${line}`
        };
      }
      
      return {
        confirmation: `${line}
   ✅ *تم تسجيل الإنجاز*
${line}

📋 *الخطوة المنجزة:* 
   ${stepName}

📌 *الخطوة القادمة:*
   ${steps[nextStep - 1].name}

🏢 *الجهة:* ${steps[nextStep - 1].dept}
⏱️ *المدة:* ${steps[nextStep - 1].duration} يوم

📊 *التقدم:* ${stepNumber}/${totalSteps}

${line}

💡 سيتم إرسال تذكير غداً
للمهمة التالية

🏛️ *Al Brazili Technologies*
${line}`
      };
    
    // ⏸️ تأجيل
    case 'delay':
      await projectRef.update({
        lastDelayAt: admin.firestore.FieldValue.serverTimestamp(),
        delayCount: admin.firestore.FieldValue.increment(1),
        lastDelayStep: stepNumber
      });
      
      await db.collection('alerts').add({
        type: projectType,
        projectId: projectId,
        message: `⏸️ تم تأجيل "${stepName}" - ${projectName}`,
        cat: 'deadline',
        date: now.toLocaleString('ar-EG'),
        createdAt: admin.firestore.FieldValue.serverTimestamp(),
        automated: true
      });
      
      return {
        confirmation: `${line}
   ⏸️ *تم تسجيل التأجيل*
${line}

📋 *الخطوة:* ${stepName}
👤 *العميل:* ${projectName}
📅 *التاريخ:* ${formatArabicDate(now)}

${line}

⚠️ *ملاحظة:*
سيتم إرسال تذكير جديد غداً

🏛️ *Al Brazili Technologies*
${line}`
      };
    
    // ⚠️ مشكلة
    case 'problem':
      await projectRef.update({
        status: 'under_review',
        lastProblem: analysis.details,
        lastProblemAt: admin.firestore.FieldValue.serverTimestamp(),
        hasProblem: true
      });
      
      await db.collection('alerts').add({
        type: projectType,
        projectId: projectId,
        message: `⚠️ مشكلة: ${analysis.details}`,
        cat: 'deadline',
        date: now.toLocaleString('ar-EG'),
        createdAt: admin.firestore.FieldValue.serverTimestamp(),
        urgent: true
      });
      
      // إشعار عاجل للمدير
      await notifyManager(
        `🚨 *مشكلة عاجلة*\n\n` +
        `👤 العميل: ${projectName}\n` +
        `📋 الخطوة: ${stepName}\n` +
        `👷 المهندس: ${reminder.engineerName}\n\n` +
        `⚠️ *التفاصيل:*\n${analysis.details}`
      );
      
      return {
        confirmation: `${line}
   ⚠️ *تم تسجيل المشكلة*
${line}

📋 *الخطوة:* ${stepName}
👤 *العميل:* ${projectName}

📝 *التفاصيل:*
${analysis.details}

${line}

🚨 *تم إشعار الإدارة فوراً*
📞 سيتم التواصل معك قريباً

🏛️ *Al Brazili Technologies*
${line}`
      };
    
    // ❓ استفسار
    case 'inquiry':
      await db.collection('communications').add({
        type: projectType,
        projectId: projectId,
        engineerId: engineerId,
        engineerName: reminder.engineerName,
        subject: 'استفسار',
        message: analysis.details,
        date: now.toLocaleString('ar-EG'),
        createdAt: admin.firestore.FieldValue.serverTimestamp(),
        autoReceived: true,
        replyText: text
      });
      
      await notifyManager(
        `❓ *استفسار جديد*\n\n` +
        `👷 من: ${reminder.engineerName}\n` +
        `👤 العميل: ${projectName}\n\n` +
        `💬 *الاستفسار:*\n${analysis.details}`
      );
      
      return {
        confirmation: `${line}
   ❓ *تم استلام استفسارك*
${line}

📋 *الخطوة:* ${stepName}

💬 *الاستفسار:*
${analysis.details}

${line}

⏰ *سيتم الرد عليك قريباً*
من فريق Al Brazili

📞 *للتواصل المباشر:*
01110798913

🏛️ *Al Brazili Technologies*
${line}`
      };
    
    // 📊 تحديث
    case 'update':
      await db.collection('communications').add({
        type: projectType,
        projectId: projectId,
        engineerId: engineerId,
        engineerName: reminder.engineerName,
        subject: 'تحديث',
        message: analysis.details,
        date: now.toLocaleString('ar-EG'),
        createdAt: admin.firestore.FieldValue.serverTimestamp(),
        autoReceived: true,
        replyText: text
      });
      
      await notifyManager(
        `📊 *تحديث جديد*\n\n` +
        `👷 من: ${reminder.engineerName}\n` +
        `👤 العميل: ${projectName}\n\n` +
        `💬 *التحديث:*\n${analysis.details}`
      );
      
      return {
        confirmation: `${line}
   📊 *تم تسجيل التحديث*
${line}

📋 *الخطوة:* ${stepName}
👤 *العميل:* ${projectName}

📝 *التحديث:*
${analysis.details}

${line}

✅ *شكراً لك على التحديث*

🏛️ *Al Brazili Technologies*
${line}`
      };
    
    // ❓ رد غير مفهوم
    default:
      await notifyManager(
        `❓ *رد غير مفهوم*\n\n` +
        `👷 من: ${reminder.engineerName}\n` +
        `👤 العميل: ${projectName}\n` +
        `📝 *الرد:*\n${text}`
      );
      
      return {
        confirmation: `${line}
   ⚠️ *لم أفهم ردك*
${line}

📋 *الخطوة:* ${stepName}

❌ لم أستطع تحليل رسالتك:
_"${text}"_

${line}

💡 *للرد الصحيح، استخدم:*

*1* ✅ تم الإنجاز
*2* ⏸️ تأجيل
*3* ⚠️ مشكلة
*4* ❓ استفسار
*5* 📊 تحديث

${line}

📞 أو تواصل: 01110798913

🏛️ *Al Brazili Technologies*
${line}`
      };
  }
}

// ═══════════════════════════════════════════════════════════
// 📢 إشعار المدير
// ═══════════════════════════════════════════════════════════

async function notifyManager(message) {
  if (!process.env.MANAGER_PHONE) {
    console.warn('⚠️ MANAGER_PHONE غير محدد');
    return;
  }
  
  try {
    await sendWhatsApp(process.env.MANAGER_PHONE, message);
    console.log('📢 تم إشعار المدير');
  } catch (error) {
    console.error('❌ فشل إشعار المدير:', error.message);
  }
}

// ═══════════════════════════════════════════════════════════
// 🧪 دالة اختبار يدوي
// ═══════════════════════════════════════════════════════════

exports.testDailyReminder = functions.https.onRequest(async (req, res) => {
  try {
    const { projectId, projectType } = req.query;
    
    if (!projectId || !projectType) {
      return res.status(400).json({ 
        error: 'مطلوب: projectId و projectType',
        example: '?projectId=xxx&projectType=buildingPermits'
      });
    }
    
    const projectDoc = await db.collection(projectType).doc(projectId).get();
    if (!projectDoc.exists) {
      return res.status(404).json({ error: 'المشروع غير موجود' });
    }
    
    const project = projectDoc.data();
    const stepNum = (project.current_step || 0) + 1;
    const message = buildDailyReminderMessage(project, stepNum, projectType);
    
    res.json({
      success: true,
      preview: message,
      project: {
        client: project.client,
        step: stepNum,
        engineer: project.engineer_id
      }
    });
    
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// ═══════════════════════════════════════════════════════════
// 🔄 دالة اختبار المحلل النصي
// ═══════════════════════════════════════════════════════════

exports.testReplyAnalyzer = functions.https.onRequest(async (req, res) => {
  const testCases = [
    'تم',
    '1',
    'خلصت',
    'تم الإنجاز',
    '✅',
    '2',
    'تأجيل',
    'بكرة',
    '3',
    'مشكلة في الرفع المساحي',
    '⚠️ مفيش مستندات',
    '4',
    'استفسار عن الرسوم',
    '❓ امتى الموعد',
    '5',
    'تحديث: تم التقديم',
    '📊 وصلت للمرحلة الثانية',
    'كلام غير مفهوم'
  ];
  
  const results = testCases.map(text => ({
    input: text,
    analysis: analyzeReply(text)
  }));
  
  res.json({ success: true, results });
});