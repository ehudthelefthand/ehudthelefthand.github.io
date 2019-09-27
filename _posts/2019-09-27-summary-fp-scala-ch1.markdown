---
layout: post
title: "สรุปสิ่งที่จำได้จากหนังสือ Functional Programming Scala จาก Manning บทที่ 1"
date: 2019-09-27
categories: jekyll update
---
เค้าบอกว่าเวลาอ่านหนังสือ ถ้าอยากให้จำได้ ให้สร้าง output เยอะๆ เพราะสมองจะได้ recall ความทรงจำ แล้วสิ่งที่อ่านมันจะอยู่นานขึ้น จึงเป็นที่มาของสรุปจากความทรงจำอันนี้ครับ

บทแรกว่าด้วยเรื่อง What and Why ของ Functional Programming

ก่อนอื่นเราามาดูตัวอย่าง code อันนี้กันก่อน

{% highlight scala %}
class Cafe {
  def buyCoffee(cc: CreditCard): Coffee = {
    val cup = new Coffee()
    cc.charge(cup.price)
    cup
  }
}
{% endhighlight %}

ดูเผินๆ ก็อ่านเข้าใจง่ายดี ไม่น่ามีปัญหาอะไร
จะซื้อกาแฟก็เอาบัตรมา คิดตังค์ และจ่ายตังค์ แล้วก็เอาการแฟไป

แต่ code ชุดนี้กลับมีปัญหาอยู่ 3 อย่าง
1. test ยาก คิดดูว่า ถ้าเราอยากรู้ว่า มันคิดตังค์ถูกจริงๆ มั้ย เราจะ test ยังไง ซึ่งเราก็จะเห็นว่า ไม่มีทางอื่นนอกจาก mock ตัว CreditCard เอา
2. ต่อยอดยาก คิดดูว่าถ้าเราอยากซื้อหลายๆ แก้ว แต่ไม่อยากแยกบิล โค้ดที่เห็นอยู่นี้จะทำได้ยาก
3. charge ทำงานมากว่า 1 อย่าง มันทั้งคิดเงิน และ process payment มันจะดีกว่ามากถ้าเราแยกมันออกจากัน

มาลอง upgrade code อีกนิด

{% highlight scala %}
class Cafe {
  def buyCoffee(cc: CreditCard, p: Payment): Coffee = {
    val cup = new Coffee()
    p.charge(cc, cup.price)
    cup
  }
}
{% endhighlight %}

โค้ดนี้ดีขึ้นอีกนิด อย่างน้อยก็แก้ปัญหาในข้อที่ 3 แต่ยังไม่แก้ปัญหาทั้งหมด เพราะถ้าจะ test ก็ยังต้อง mock อยู่ดี
วิธีการของ Functional ที่จะเอามาแก้ปัญหานี้ก็คือ เปลี่ยนจากสิ่งที่เป็น side-effect ให้เป็น return value

ก่อนจะไปที่ท่า Functional เรามาเข้าใจคำว่า side-effect ก่อน
code ที่เป็น side-effect ก็คือ
1. code ที่ไม่ return value
2. code ที่ไปเปลี่ยน state ของ program หรือ เรียก dependency นอก โดยที่เราควบคุมไม่ได้
สังเกตุว่า ถ้าเรา charge 2 รอบ มันอาจจะสำเร็จ หรือไม่สำเร็จก็ได้ แปลว่ามันมีตัวแปรอื่นที่เราควบคุมไม่ได้อยู่


