# 🧠 Bangla Drive OCR (by RONY)

**এক ক্লিকে গুগল ড্রাইভ বেসড OCR সলিউশন — যত ইমেজ দেন, সব টেক্সট একটা ফাইলে এনে দিবো।**  
কোনো ঝামেলা নাই। কোনো API key লাগে না।  
শুধু গুগল ড্রাইভের পারমিশন দিলেই হবে।

---

## 🔍 Problem:
আমরা অনেক সময় মোবাইলে ছবি তুলি — hand-written note, printed text, বা কাগজে লেখা কিছু — যেটা আমরা পরে digital text বানাতে চাই।  
কিন্তু Google Drive দিয়ে এক এক করে ম্যানুয়ালি করা বিরক্তিকর + টাইম ওয়েস্ট।

---

## ✅ My Solution:
এই প্রজেক্টে আমি এমন একটা ক্লাস বানাইলাম যেটা:

- Google Drive API ইউজ করে  
- যেকোনো ইমেজ ফাইল OCR করে (Bangla/English দুইটাই সুন্দরভাবে কাজ করে)  
- সব ইমেজের টেক্সট একসাথে **একটা টেক্সট ফাইলে** (default: `combined_ocr.txt`) সেভ করে  

একবার সব সেটআপ করলেই, এরপর শুধু **step-1 (upload)** আর **step-2 (run)** করলেই হবে।

---

## ⚙️ How it Works (Behind the Scene):

1. প্রথমে ডিপেন্ডেন্সি ইনস্টল করতে হবে।  
👉 এটা একবার করলেই হয়। মাঝে মাঝে রিস্টার্ট দিতে বলবে, ইগনোর করতে পারেন।

```python
!pip install --upgrade google-api-python-client google-auth google-auth-oauthlib google-auth-httplib2
