تطبيق مواقيت الصلاة - الإسماعيلية
ده تطبيق ويب بيتثبت على الموبايل زي تطبيق الأندرويد. بيحسب مواعيد الصلاة لمدينة الإسماعيلية بطريقة الهيئة المصرية العامة للمساحة، ويشتغل من غير إنترنت بعد أول فتح.
الفكرة
التطبيق مكوّن من أربعة ملفات: صفحة رئيسية، ملف خدمة للعمل بدون نت، ملف إعدادات التثبيت، وأيقونة. كل الملفات في نفس الفولدر.
الملف الأول: index.html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#0b5d3b">
<link rel="manifest" href="manifest.json">
<link rel="icon" href="icon.svg">
<title>مواقيت الصلاة</title>
<style>
  body{margin:0;font-family:sans-serif;background:#0b5d3b;color:#fff;text-align:center}
  h1{margin:16px 0 4px;font-size:22px}
  #date{opacity:.8;font-size:14px}
  #next{background:#fff;color:#0b5d3b;margin:16px;padding:16px;border-radius:14px;font-size:20px;font-weight:bold}
  #count{font-size:28px;margin-top:6px}
  .row{display:flex;justify-content:space-between;background:rgba(255,255,255,.12);margin:8px 16px;padding:14px;border-radius:10px;font-size:18px}
  .row.active{background:#d4a017;color:#000;font-weight:bold}
</style>
</head>
<body>
<h1>مواقيت الصلاة - الإسماعيلية</h1>
<div id="date"></div>
<div id="next"><div id="nextName"></div><div id="count"></div></div>
<div id="list"></div>
<script>
const LAT=30.5965, LON=32.2715, TZ=2;
const FAJR_ANGLE=19.5, ISHA_ANGLE=17.5;
const rad=d=>d*Math.PI/180, deg=r=>r*180/Math.PI;

function sunPos(jd){
  const D=jd-2451545.0;
  const g=rad((357.529+0.98560028*D)%360);
  const q=(280.459+0.98564736*D)%360;
  const L=rad((q+1.915*Math.sin(g)+0.020*Math.sin(2*g))%360);
  const e=rad(23.439-0.00000036*D);
  const decl=Math.asin(Math.sin(e)*Math.sin(L));
  const RA=deg(Math.atan2(Math.cos(e)*Math.sin(L),Math.cos(L)))/15;
  const eqt=q/15-((RA+24)%24);
  return {decl, eqt:((eqt+12)%24)-12};
}

function hourAngle(angle,decl){
  const lat=rad(LAT);
  const c=(-Math.sin(rad(angle))-Math.sin(lat)*Math.sin(decl))/(Math.cos(lat)*Math.cos(decl));
  return deg(Math.acos(Math.max(-1,Math.min(1,c))))/15;
}

function compute(date){
  const y=date.getFullYear(), m=date.getMonth()+1, d=date.getDate();
  const jd=Date.UTC(y,m-1,d,12)/86400000+2440587.5;
  const {decl,eqt}=sunPos(jd);
  const noon=12+TZ-LON/15-eqt;
  const lat=rad(LAT);
  const asrAlt=deg(Math.atan(1/(1+Math.tan(Math.abs(lat-decl)))));
  return {
    'الفجر': noon-hourAngle(FAJR_ANGLE,decl),
    'الشروق': noon-hourAngle(0.833,decl),
    'الظهر': noon+0.025,
    'العصر': noon+hourAngle(-asrAlt,decl)*-1+0,
    'المغرب': noon+hourAngle(0.833,decl)+0.0,
    'العشاء': noon+hourAngle(ISHA_ANGLE,decl)
  };
}

function fmt(h){
  h=((h%24)+24)%24;
  let hh=Math.floor(h), mm=Math.round((h-hh)*60);
  if(mm===60){hh++;mm=0}
  const p=hh>=12?'م':'ص';
  const h12=hh%12===0?12:hh%12;
  return h12+':'+String(mm).padStart(2,'0')+' '+p;
}

function render(){
  const now=new Date();
  const t=compute(now);
  document.getElementById('date').textContent=now.toLocaleDateString('ar-EG',{weekday:'long',year:'numeric',month:'long',day:'numeric'});
  const nowH=now.getHours()+now.getMinutes()/60+now.getSeconds()/3600;
  let next=null;
  for(const [n,h] of Object.entries(t)){ if(h>nowH){next=[n,h];break;} }
  if(!next){ const tm=compute(new Date(now.getTime()+86400000)); next=['الفجر',tm['الفجر']+24]; }
  let diff=next[1]-nowH, hh=Math.floor(diff), mm=Math.floor((diff-hh)*60), ss=Math.floor(((diff-hh)*60-mm)*60);
  document.getElementById('nextName').textContent='الصلاة القادمة: '+next[0];
  document.getElementById('count').textContent=String(hh).padStart(2,'0')+':'+String(mm).padStart(2,'0')+':'+String(ss).padStart(2,'0');
  document.getElementById('list').innerHTML=Object.entries(t).map(([n,h])=>'<div class="row'+(n===next[0]?' active':'')+'"><span>'+n+'</span><span>'+fmt(h)+'</span></div>').join('');
}
render(); setInterval(render,1000);
if('serviceWorker' in navigator) navigator.serviceWorker.register('sw.js');
</script>
</body>
</html>
الملف الثاني: manifest.json
{
  "name": "مواقيت الصلاة - الإسماعيلية",
  "short_name": "الصلاة",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#0b5d3b",
  "theme_color": "#0b5d3b",
  "icons": [
    { "src": "icon.svg", "sizes": "any", "type": "image/svg+xml", "purpose": "any" }
  ]
}
الملف الثالث: sw.js
const C='prayer-v1';
self.addEventListener('install',e=>{
  e.waitUntil(caches.open(C).then(c=>c.addAll(['./','./index.html','./manifest.json','./icon.svg'])));
});
self.addEventListener('fetch',e=>{
  e.respondWith(caches.match(e.request).then(r=>r||fetch(e.request)));
});
الملف الرابع: icon.svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
  <rect width="512" height="512" rx="96" fill="#0b5d3b"/>
  <circle cx="256" cy="256" r="130" fill="#d4a017"/>
  <circle cx="300" cy="230" r="110" fill="#0b5d3b"/>
</svg>
طريقة الرفع والتثبيت على الموبايل
اعمل حساب مجاني على موقع github.com.
اضغط على علامة الزائد وبعدين New repository، واكتب له اسم مثلاً prayer، واختار Public، واضغط Create.
اضغط على Add file وبعدين Create new file، واكتب اسم الملف index.html، والصق الكود، واضغط Commit. كرر نفس الخطوة للملفات الثلاثة التانية.
ادخل على Settings وبعدين Pages، واختار Branch اسمه main، واضغط Save.
استنى دقيقة أو اتنين، وهيظهر لك رابط الموقع بتاعك.
افتح الرابط من كروم على موبايل الأندرويد.
اضغط على النقط التلاتة فوق، واختار Install app أو Add to Home screen.
التطبيق هيظهر على شاشة الموبايل بأيقونته، وتفتحه زي أي تطبيق.
ملاحظات مهمة
المواعيد بتتحسب بالمعادلات الفلكية، فممكن يكون فيه فرق دقيقة أو اتنين عن التقويم الرسمي. قارن النتيجة بالمواعيد الرسمية في أول يوم، ولو فيه فرق ثابت قولي وأظبطه لك.
الحساب مظبوط على إحداثيات الإسماعيلية وتوقيت القاهرة الشتوي. لو التوقيت الصيفي اشتغل في مصر، لازم نعدل رقم التوقيت في أول الكود.
