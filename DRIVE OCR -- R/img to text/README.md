# 🧠 Bangla Drive OCR (গুগল ড্রাইভ বেসড বাংলা OCR টুল)

এই টুলের মাধ্যমে আপনি একাধিক **JPEG ফরম্যাটের ইমেজ** থেকে টেক্সট এক্সট্রাক্ট করতে পারবেন, Google Drive-এর বিল্ট-ইন OCR সিস্টেম ব্যবহার করে।  
সব ইমেজের লেখা একত্রে একটি `.txt` ফাইলে জমা হবে।

---

## 🔧 কীভাবে এই কোড কাজ করে (Behind the Scenes)

### ✅ Step 1: প্রয়োজনীয় লাইব্রেরি ইনস্টল

```python
!pip install --upgrade google-api-python-client google-auth google-auth-oauthlib google-auth-httplib2
```

> এই লাইব্রেরিগুলো গুগলের অফিসিয়াল API প্যাকেজ — Google Drive-এর সাথে কানেক্ট করার জন্য দরকার হয়।

---

### 🔐 Step 2: গুগল অ্যাকাউন্ট দিয়ে লগইন

```python
from google.colab import auth
auth.authenticate_user()
```

> এই লাইনে আপনার গুগল অ্যাকাউন্ট দিয়ে অথেনটিকেশন করতে হবে। একটা পপআপ আসবে, লগইন করে সব পারমিশন Allow দিতে হবে।

---

### 🔌 Step 3: Google Drive API কানেকশন

```python
from googleapiclient.discovery import build
service = build('drive', 'v3')
```

> এই `service` অবজেক্ট দিয়ে আমরা Google Drive API-তে ছবি আপলোড, টেক্সট এক্সট্রাক্ট, আর পরে ফাইল ডিলিট করব।

---

### 📸 Step 4: ছবি আপলোড করা

```python
from google.colab import files
uploaded = files.upload()
```

> আপনি একাধিক JPEG ছবি এখানে আপলোড করতে পারেন। এই ছবিগুলো থেকেই টেক্সট এক্সট্রাক্ট করা হবে।

---

### 🧠 Step 5: DriveOCR ক্লাস (মূল কাজের জায়গা)

#### ক্লাস ইনিশিয়ালাইজ:

```python
class DriveOCR:
    def __init__(self, file_list):
        self.file_list = file_list
```

এই ক্লাস ছবির নামের লিস্ট নেয় এবং পরবর্তী OCR প্রক্রিয়া চালায়।

#### মূল OCR প্রসেস:

```python
def extract_all_to_single_file(self, output_filename="ocr_output.txt"):
```

এই ফাংশন যা যা করে:

1. **ছবি Google Drive-এ আপলোড করা**  
   প্রতিটি ছবি Google Docs হিসেবে আপলোড করা হয়, যাতে Google স্বয়ংক্রিয়ভাবে OCR চালায়।

2. **Plain Text হিসেবে এক্সপোর্ট করা**  
   Google Drive ইমেজ থেকে লেখা বের করে সেটাকে `.txt` হিসেবে এক্সপোর্ট করা হয়:

   ```python
   request = service.files().export_media(fileId=file_id, mimeType='text/plain')
   ```

3. **টেক্সট ডাউনলোড করা**  
   এক্সপোর্ট করা লেখা Python-এ নিয়ে আসা হয় এবং decode করা হয়।

4. **একত্রে সংরক্ষণ করা**  
   প্রতিটি ছবির লেখা একত্র করে একটি `.txt` ফাইলে লেখা হয়:

   ```
   combined_ocr.txt
   ```

5. **Google Drive থেকে ফাইল ডিলিট করা**  
   কাজ শেষ হলে Google Drive থেকে ওই আপলোড করা ফাইল মুছে ফেলা হয়:

   ```python
   service.files().delete(fileId=file_id).execute()
   ```

---

## 💾 Output ফাইল

সব লেখা একত্রে নিচের ফাইলে জমা হয়:

```
combined_ocr.txt
```

প্রতিটি সেকশনে ফাইলের নাম উল্লেখ থাকবে, যেমন:

```
--- OCR from: image1.jpg ---
[ইমেজ থেকে এক্সট্রাক্ট করা লেখা]

--- OCR from: image2.jpg ---
[পরবর্তী ইমেজের লেখা]
```

---

## 🔁 কীভাবে পুনরায় ব্যবহার করবেন

- ✅ প্রথমবার: সব কোড ব্লক একবার রান করতে হবে।
- ♻️ পরবর্তীতে: শুধুমাত্র Step-1 (ছবি আপলোড) ও Step-2 (DriveOCR এক্সিকিউশন) রান করলেই চলবে।

গুগল অথেনটিকেশন একবার করলে সেটা সেশনের মধ্যে স্মরণে থাকবে।

---

## 📌 গুরুত্বপূর্ণ বিষয়

- Google Drive এর **বিল্ট-ইন OCR ইঞ্জিন** ব্যবহার করা হয়েছে।
- শুধু **JPEG ইমেজ** ফরম্যাট সাপোর্ট করে।
- আউটপুট ফাইল **UTF-8** এনকোডেড `txt` ফরম্যাটে থাকে।
- কোনো থার্ড-পার্টি OCR (যেমন Tesseract) ব্যবহার করা হয়নি — একদম সার্ভারলেস ও লাইটওয়েট সলিউশন।

---

## 📥 ব্যবহার উদাহরণ

ধরুন আপনি বাংলা হাতে লেখা নোট, বা বইয়ের স্ক্রিনশট JPEG ফরম্যাটে স্ক্যান করেছেন।  
এই টুলে সেগুলো আপলোড করলে অটোমেটিক ভাবে লেখা বের করে আপনাকে `.txt` ফাইলে দিয়ে দেবে।  
চাইলে সেই টেক্সট কোথাও পেস্ট করুন, সংরক্ষণ করুন বা অন্য কাজে ব্যবহার করুন।

---



💻 তৈরি করেছেন: **RONY**
