# 🤖 Auto Sync Runner Images README

Tự động đồng bộ file `README.md` từ repo [`actions/runner-images`](https://github.com/actions/runner-images) về repo này, kèm **commit message sinh bởi AI (Gemini)** mô tả nội dung thay đổi bằng tiếng Việt.

---

## ✨ Tính năng

- 📥 **Tự động tải** `README.md` mới nhất từ `actions/runner-images` về thư mục `runner-images/`
- 🔍 **Phát hiện thay đổi** — chỉ commit khi có nội dung mới, không commit thừa
- 🧠 **Sinh commit message bằng AI** — phân tích git diff và mô tả thay đổi bằng tiếng Việt qua Gemini API
- 🔄 **Fallback model tự động** — thử lần lượt nhiều model Gemini nếu model trước bị lỗi:
  `gemini-flash-lite-latest` → `gemini-3.1-flash-lite` → `gemini-3.5-flash-lite` → `gemini-2.5-flash-lite`
- 📡 **Fallback khi không có API key** — tự động lấy commit message gốc từ `actions/runner-images` qua GitHub public API
- ⏱️ **Chạy định kỳ** mỗi 5 ngày một lần (hoặc chạy thủ công)

---

## ⚙️ Cách hoạt động

```
Trigger (schedule / push / manual)
        │
        ▼
Checkout repo
        │
        ▼
curl → tải README.md từ actions/runner-images
        │
        ▼
git status → có thay đổi?
        │ Không → dừng
        │ Có
        ▼
┌─────────────────────────────────────────┐
│         Sinh commit message             │
│                                         │
│  Có GEMINI_API_KEY?                     │
│    ├─ Có → git diff → gọi Gemini API   │
│    │        (fallback qua nhiều model)  │
│    │        → commit message tiếng Việt │
│    │                                    │
│    └─ Không → GitHub public API         │
│               lấy commit gốc của        │
│               actions/runner-images     │
│               → "sync: <commit gốc>"   │
│               (fallback: default msg)   │
└─────────────────────────────────────────┘
        │
        ▼
git commit + git push
```

---

## 🚀 Kích hoạt workflow

| Cách kích hoạt | Mô tả |
|---|---|
| ⏰ **Tự động** | Chạy lúc `00:00 UTC`, mỗi 5 ngày |
| 📝 **Push** | Khi file `runner-images.yml` thay đổi |
| 🖱️ **Thủ công** | GitHub → Actions → **Run workflow** |

---

## 🔑 Cấu hình

Thêm secret sau vào repo (**Settings → Secrets and variables → Actions**):

| Secret | Mô tả |
|---|---|
| `GEMINI_API_KEY` | API key của Google Gemini (lấy tại [aistudio.google.com](https://aistudio.google.com/app/apikey)) |

> **Không có API key vẫn hoạt động bình thường** — workflow sẽ tự lấy commit message từ repo gốc `actions/runner-images` qua GitHub public API (giới hạn ~60 req/giờ, không cần token).

---

## 💬 Ví dụ commit message

| Trường hợp | Commit message |
|---|---|
| Có Gemini API | `Cập nhật thông tin phiên bản Ubuntu 24.04 và macOS 15` |
| Không có API (lấy từ repo gốc) | `sync: Add macOS 15 Sequoia runner image` |
| Fallback cuối cùng | `update runner-images/README.md` |

---

## 📁 Cấu trúc

```
.
├── .github/
│   └── workflows/
│       └── runner-images.yml   # Workflow chính
└── runner-images/
    └── README.md               # File được đồng bộ tự động
```

---

## 📄 Nguồn dữ liệu

File được tải từ:
```
https://raw.githubusercontent.com/actions/runner-images/refs/heads/main/README.md
```
