---
layout: post
title: "ความงดงามของ JSX"
date: 2019-09-04
categories: jekyll update
---

ครั้งแรกที่ผมเห็น JSX ผมร้อง ว้าววว ซึ่งแปลได้ว่า "อะไรของมึงวะเนี่ย โคตรไม่เข้ากันเลย เอา HTML มาไว้ใน JS เนี่ยนะ บ้าเปล่า โค้ดปนกันมั่วตายห่า แถม syntax โคตร magic เลย ไม่ไหวๆ รับไม่ได้"

แต่เมื่อผมลองให้เวลามันได้วิ่งเล่นอยู่ในความคิดซักพัก ผมพบว่ามันโคตร make sense เลย แถม JSX เนี่ย ไม่ได้ magic อะไรเลย วิธีการทำงานของมัน จริงๆ แล้วเข้าใจง่ายมากๆ

ผมจะลองเล่าดูนะ ว่ามัน make sense ยังไง
ก่อนอื่น ลองจินตาการว่า เราจะทำ todo app ซักอัน และสมมติว่าเรายังไม่รู้จัก client side template
เวลาเราจะวาดหน้า HTML ที่มัน dynamic เนี่ย เราจะต้องใช้ DOM API ค่อยๆ วาดมันขึ้นมา ฟังดูเหมือนเยอะ แต่จริงๆ method หนึ่งเดียวที่เราใช้ก็คือ

var element = document.createElement(tagName[, options]);

Parameter แรกคือ tag ที่เราต้องการ ส่วน options ข้ามไปก่อน มันเกี่ยวกับ web component ซึ่งขอไม่พูดถึงครับ
ทีนี้พอได้ element มาแล้ว
ส่วนใหญ่เราก็เอามันมา

1. แปะ attr เพิ่ม
2. ใส่ content ซึ่งจะเป็น text หรือ element อีกอันซ้อนเข้าไป
3. ใส่ event listener เพื่อรองรับ user interaction

หมดละ เท่าที่ผมรู้ นี่คือทั้งหมดที่จำเป็นละ ในการสร้าง HTML element 1 ตัว โคตร simple เลย (ขอบคุณ HTML Spec)

ซึ่งถ้าทำ todo app ในส่วนของ todo item ก็น่าได้ code ประมาณนี้

{% highlight javascript %}
var itemData = { id: 1, text: "buy milk", done: false };
var item = document.createElement("div");
item.classList.add("item");
item.textContent = itemData.text;
item.addEventListener("click", function() {
// do something with item here
});
{% endhighlight %}

ทีนี้ตอน render เราก็จะเอามัน แปะ เข้าไปที่ root element ซักตัวในหน้า HTML ผ่าน DOM API เหมือนเดิม
ซึ่งรอบนี้เราจะใช้ method ชื่อว่า

{% highlight javascript %}
element.appendChild(aChild);
{% endhighlight %}

โค้ดเต็มๆ ก็ประมาณว่า

{% highlight javascript %}
var todoApp = ... // แหะๆ เต็มจริงๆ เดี๋ยวยาว
var root = document.getElementById("app");
root.appendChild(todoApp);
{% endhighlight %}

นึกภาพว่าตัวแปร todoApp มันก็จะเกิดจากการประกอบตัวกันของ element ต่างๆ ซ้อนๆ กันหลายๆ ชิ้น ซึ่งแต่ละชิ้นมีหน้าที่เจาะจง การมองภาพแบบนี้ เราเรียกว่า component นั่นแหละ

ตัดภาพย้อนกลับไปเล็กน้อย ตรง todo item นั่นก็เป็น component หนึ่งอัน ซึ่งเราจะเห็นว่า ภาพของ UI component 1 ตัว เนี่ยมักจะมี Presenation กับ Behavior pack รวมไปด้วยกันเสมอเลย

ถึงจุดนี้ มันคงจะดีไม่น้อย ถ้าหากว่า เราสามารถเขียนทุกอย่างให้มันสั้นลง เข้าใจง่ายขึ้น เช่น

ไอ่โค้ดเนี่ย

{% highlight javascript %}
var element = document.createElement(tagName[, options]);
{% endhighlight %}

ถ้าเขียน HTML ไปเลย น่าจะดีกว่านะ เข้าใจง่ายกว่ามาก จึงเป็นที่มาของ client side template
คำถามถัดมาคือ เราควรเอา template ไปไว้ไหนดี
fast forward กาลเวลาผ่านมาเราก็เห็นคนไปกันหลายท่า
เช่น

jQuery อันนี้ไม่ใช่ client side template แต่เป็นผู้ริเริ่มลดความซับซ้อนด้วยการใช้ syntax \$() เพื่อลด code ในการสร้าง element ให้สั้นลงมาก และเนื่องจากเป็นเจ้าแรกๆ คนเลยชอบมันมาก ฮิตสุดเลย

Handlebar กับ Mustache สองอันนี้มีความเป็น client side template เต็มตัวมาก และไปท่าคล้ายๆกันคือ แยก template ออกไปไว้อีกที่จะได้ดู clean เช่น embed มันไว้ที่ script tag ใน HTML พอจะเรียกใช้ก็ไป getElementById มา แล้ว compile มันทีนึง แล้วค่อยสั่ง render ข้อดีคือ html กับ js แยกกัน clean ดี ข้อเสียคือ overhead เยอะ

และที่ไม่พูดถึงไม่ได้คือ Angular นั่นเอง พร้อมกับการมาของคำว่า “Data Binding” มันคือที่สุดของการ pack รวม และทำให้เราเห็นภาพ component ชัดมาก ยิ่งใน version หลังๆ ยิ่งชัด generate component ออกมาได้ 3 ไฟล์เลย html, css และ ts ครบ

และเจ้าอื่นๆ อีกมากมาย แต่ไม่ว่าจะเจ้าไหนก็แล้วแต่ การมองภาพของสิ่งที่เรียกว่า component เนี่ย มีความคล้ายกันมาก คือ Presentation กับ Behavior ของ Presentation ตัวนั้น ห่อรวมไปด้วยกันเสมอ

จากที่ผมยก ต.ย. มาเราก็เห็นอยู่แล้วว่า คนเราแม้จะแก้ปัญหาเดียวกัน มันก็ไปได้หลายท่า เมื่อมีคนคิดแยก client side template มันก็ต้องมีคนคิดว่า รวมไปเลยสิวะ!

ใช่! นั่นแหละคือ JSX ในเมื่อมันก็ต้องไปด้วยกันอยู่แล้ว เอารวมกันไปเลยสิในไฟล์เดียวกันนี่แหละ และตรงจุดนี้เองที่ผมเริ่มร้อง อ๋ออออ โคตร make sense เลยวะ

ก่อนจะพูดเรื่อง กลไกของ JSX ขอไปปูเรื่อง React กันก่อน เนื่องจาก JSX มันเกิดไม่ได้ถ้าไม่มี React และถ้าจะพูดถึงความสวยงามของ React ผมอยากจะตัดภาพกลับไปตอนต้น นั่นก็คือ โค้ดการสร้าง element หนึ่งตัว มันประกอบไปด้วย tagName, attr, content
React จึงรวบขั้นตอนการสร้าง element ที่ทำทั้ง 3 อย่างนั้น ให้อยู่ในขั้นตอนเดียว ถึงตรงเราอาจจะอุทานว่า เหี้ย! เหมือน jQuery เลย ผมก็ไม่รู้ว่าเป็นความบังเอิญมั้ย แต่ช่างมันเถอะ สุดท้ายมลมนุษยชาติเราก็มาจบลงที่ interface นี้ครับ

{% highlight javascript %}
React.createElement(
type, // ชื่อ tag
[props], // attr
[...children] // content
)
{% endhighlight %}

จบ! กรุณายืนขึ้นตบมือด้วยครับ

เดี๋ยว! ยังไม่จบ กลับมาก่อน

ความงามของมันคือ มันครบมาก และสังเกตุว่า แม้แต่ชื่อ method ยังเป็นชื่อเดียวกันกับ DOM API เลย คือ อารมณ์แบบ “กูนี่แหละ DOM API ตัวจริง!” 555

และอีกหนึ่ง method ที่งดงามไม่แพ้กันก็คือ

{% highlight javascript %}
ReactDOM.render(element, container[, callback])
{% endhighlight %}

แทนที่การ appendChild เพราะมันไม่ใช่แค่การเอา element ไปแปะกับ DOM แบบสั่วะๆ แต่มัน minimize การ update ให้เราถึงขีดสุดจึงทำให้การ render มันเร็วมาก

ถึงตรงนี้ เอาสิ่งที่ทดไว้เมื่อกี้ออกมาครับ

การเกิดขึ้นมาของ JSX นั้นนอกจากจะเป็นเรื่องแนวคิดแล้ว มันเกิดขึ้นมาได้เพราะมีฐานที่ดีอย่าง Babel ด้วย ซึ่งจริงๆ แล้วความอัศจรรย์มันอยู่ที่ไอ่ Babel นี่แหละ JSX เพียงต่อจากสิ่งที่มีอยู่แล้ว ถ้าหากสิ่งที่ Babel ทำคือการ transpile จาก JS code ชุดนึงที่ version สูงกว่าเป็นไป JS code อีกชุดนึงที่ version ต่ำกว่า

หรือถ้าให้ยกตัวอย่างง่ายๆ เลยคือ จากสิ่งนี้

{% highlight javascript %}
() => {}
{% endhighlight %}

เป็นสิ่งนี้

{% highlight javascript %}
(function () {});
{% endhighlight %}

เจ้า JSX เองก็ทำคล้ายๆ กันเลย คือ แปลงจากสิ่งนี้

{% highlight html %}

<div className="text">hello</div>
{% endhighlight %}

ไปเป็นสิ่งนี้

{% highlight javascript %}
React.createElement("div", {
className: "text"
}, "hello");
{% endhighlight %}

ใช่แล้วครับ มันแปลงจาก HTML ไปเป็น React.createElement นั่นเอง

เราจะเห็นว่า สิ่งที่ JSX ทำ ประกอบกับ React.createElement และ ReactDOM.render ช่วยลดความซับซ้อนของการ render หน้า Application ลงมาก และช่วยลดความซับซ้อนของ Code ในตัว Application ลงด้วย เพราะ Logic ที่เกี่ยวข้องกับ Presentation นั้นๆ มันถูกห่อไว้ได้วยกันอย่างกลมกลืน

และนี่คือสิ่งที่ผมได้เรียนรู้จากการใช้งาน JSX และ React ครับ
