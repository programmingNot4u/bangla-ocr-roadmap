
# 🧾 PDF to OCR Text Extractor using Google Drive API (Colab Edition by RONY)

একটা **PDF ফাইল আপলোড দিলেই** তার প্রতিটা পেজ থেকে **টেক্সট এক্সট্রাক্ট** করবে — কোনো API key  ঝামেলা নাই।  
সবকিছু Google Drive OCR দিয়ে হবে, সব ফাইল Safe zone-এ।

---

## 📦 Features:

- PDF ➡ Image ➡ OCR ➡ Combined `.txt` output  
- Google Drive API দিয়ে OCR (Bangla + English supported)  
- সব পেজ এর লেখা একটাই ফাইলে সেভ হয় (`pdf_ocr_output.txt`)  
- Auto delete temporary Drive files  

---

## ⚙️ Step by Step Usage:

### 🛠️ Step-0: Install Required Packages

```python
# আমাদের প্রয়োজনীয় সব ডিপেন্ডেন্সি গুলা ইনস্টল করে নিচ্ছি
!pip install pdf2image
!sudo apt-get install poppler-utils
!pip install --upgrade google-api-python-client google-auth google-auth-oauthlib google-auth-httplib2


> 📌 নোট: ইনস্টল করার পরে সেশন রিস্টার্ট করতে বললে Ignore করতে পারেন।

```

### 🔐 Step-1: Google Drive Authentication & Import Libraries

```python
from google.colab import auth
import os
import io
from PIL import Image
from pdf2image import convert_from_path
from googleapiclient.discovery import build
from googleapiclient.http import MediaFileUpload, MediaIoBaseDownload

auth.authenticate_user()
```

> গুগল অথেনটিকেশন পপ-আপ আসবে, সব পারমিশন একদম দিয়ে দিন।

---

### 💻 Step-2: Define `DriveOCR` Class

```python
class DriveOCR:
    def __init__(self):
        self.service = build('drive', 'v3')

    def process_image(self, filepath):
        mime_type = 'application/vnd.google-apps.document'
        file_metadata = {'name': filepath, 'mimeType': mime_type}
        file = self.service.files().create(
            body=file_metadata,
            media_body=MediaFileUpload(filepath, mimetype='image/jpeg'),
            fields='id'
        ).execute()

        file_id = file['id']
        request = self.service.files().export_media(fileId=file_id, mimeType='text/plain')

        fh = io.BytesIO()
        downloader = MediaIoBaseDownload(fh, request)
        done = False
        while not done:
            status, done = downloader.next_chunk()
            print(f"{filepath}: Download {int(status.progress() * 100)}%")

        # Cleanup temporary file from Drive
        self.service.files().delete(fileId=file_id).execute()
        return fh.getvalue().decode()
```

---

### 📤 Step-3: Upload PDF File

```python
from google.colab import files
uploaded = files.upload()  # Upload your PDF file here
pdf_filename = list(uploaded.keys())[0]
print(f"Uploaded PDF: {pdf_filename}")
```

---

### 🖼️ Step-4: Convert PDF Pages to Images

```python
# Create folder to store images
os.makedirs("pdf_pages", exist_ok=True)

# Convert PDF to image list (PIL Images)
images = convert_from_path(pdf_filename)

image_paths = []

for i, img in enumerate(images):
    path = f"pdf_pages/page_{i+1}.jpg"
    img.save(path, "JPEG")
    image_paths.append(path)

print(f"Converted {len(image_paths)} pages to images.")
```

---

### 🤖 Step-5: Run OCR on All Images and Combine Text

```python
ocr = DriveOCR()
combined_text = ""

for path in image_paths:
    print(f"OCR: {path}")
    text = ocr.process_image(path)
    combined_text += f"\n--- Text from {os.path.basename(path)} ---\n{text.strip()}\n"
```

---

### 💾 Step-6: Save Combined Text to File and Download

```python
output_file = "pdf_ocr_output.txt"
with open(output_file, "w", encoding="utf-8") as f:
    f.write(combined_text)

from google.colab import files
files.download(output_file)
```

---

## 🔁 Important Notes:

* প্রথমবারের জন্য **সবগুলো কোড ব্লক একবার করে রান করতে হবে**।
* এরপর থেকে নতুন PDF এর জন্য Step-3 থেকে Step-6 রিপিট করলেই হবে।
* দ্বিতীয় বার থেকে অবশ্যই আগের জেনেরেট করা ইমেজ ফাইল **"pdf\_pages"** ফোল্ডার **মুছে ফেলতে** ভুলবেন না, না হলে নতুন পেজগুলো মিক্স হয়ে যাবে।
* PDF pages থেকে এক এক করে ইমেজ তৈরি ও OCR করার পুরো প্রসেস automated।

---

## 🙌 Why This?

PDF থেকে টেক্সট বের করা অনেক সময় ঝামেলার — special করে Bangla OCR করলে তো আরো জটিলতা।
এই কোডটা দিয়ে Google Drive এর পাকা OCR engine ইউজ করে সহজে, ঝামেলাহীন, Bangla-সহ যেকোনো PDF থেকে text extract করা যাবে।
Google Colab ব্যবহার করলে আপনি বিনামূল্যে cloud-এ কাজ করতে পারবেন।

---

## 🤝 Contribute / Issues?

যদি কোনো সমস্যা হয় বা আপগ্রেড প্রস্তাব থাকে, GitHub এ issue অথবা pull request দিন।
আমি যত দ্রুত পারি রেসপন্স দিবো।

---

## ✍️ Author:

**RONY**
Coder | Learner | Problem Solver
---

