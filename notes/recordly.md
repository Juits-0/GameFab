# Recordly — inceleme notları

Kaynak: https://github.com/webadderallorg/recordly (incelenen commit `4fda917`, 4 Ekim 2026, sürüm 1.4.0)

## Nedir?
Açık kaynak **ekran kaydedici + video editörü** (Electron masaüstü uygulaması). Demo, tanıtım ve
walkthrough videoları için: otomatik zoom, cursor efektleri, arka plan/çerçeve stilleri, webcam
balonu, timeline düzenleme, MP4/GIF export. macOS 14+, Windows 10 (19041+), Linux.
Başlangıçta OpenScreen projesinin fork'u.

## Lisans — ÖNEMLİ
**AGPL-3.0** + ek şartlar:
- Kod değiştirilip dağıtılırsa ya da bir servis (SaaS) için kullanılırsa **tüm kaynak kod** AGPL ile açılmak zorunda.
- "Recordly" adı/markası kendi projende kullanılamaz.
- Türetilmiş kodda Recordly'ye **hem arayüzde hem repoda** atıf zorunlu.

GameFab'e Recordly kodu kopyalanırsa GameFab de AGPL olur. Sadece uygulamayı **kullanmak** (oyun
tanıtım videosu çekmek) serbest ve şart doğurmaz.

## Teknoloji
- Electron 43 + React 19 + Vite + TypeScript, Tailwind 4, HeroUI
- Lint/format: Biome; test: Vitest + Playwright
- ffmpeg-static / ffprobe-static (video işleme), uiohook-napi (global klavye/fare dinleme)
- Supabase (hesap/giriş), Cloudflare Worker (R2 + D1) ile paylaşım servisi (`services/recordly-share`)
- Native yardımcılar:
  - macOS: ScreenCaptureKit (Swift) — gerçek imleci gizleyebiliyor
  - Windows: Windows Graphics Capture (C++), WASAPI ses, GPU export, NVIDIA CUDA compositor
  - Linux: Electron capture (imleç gizleme yok, sistem sesi için PipeWire)

## Whisper bağlantısı
Recordly **whisper.cpp v1.8.4**'ü kendi içinde derliyor (`scripts/build-whisper-runtime.mjs`).
Bununla **otomatik altyazı** üretiyor (`src/components/video-editor/captions/useAutoCaptionController.ts`,
`electron/ipc/captions`). Yani ayrıca whisper kurmaya gerek yok, uygulama kendi runtime'ını taşıyor.

## Klasör yapısı (özet)
| Yol | İçerik |
|---|---|
| `electron/` | Ana süreç: pencereler, IPC, kayıt, export, updater, native helper'lar |
| `electron/ipc/` | captions, cursor, export, ffmpeg, project, recording, settings handler'ları |
| `electron/native/` | Swift / C++ yakalama ve GPU yardımcıları |
| `src/components/video-editor/` | Editör arayüzü (timeline, captions, ayarlar, proje kaydetme) |
| `services/recordly-share` | Paylaşım linki servisi (Cloudflare Worker) |
| `services/supabase` | Kimlik doğrulama, migration'lar, edge function'lar |
| `scripts/` | Native build, whisper build, release, smoke test betikleri |
| `EXTENSIONS.md` | Eklenti API'si |

## Eklenti sistemi
Eklentiler editörde çalışır, izin tabanlı API kullanır. En küçük eklenti:
`recordly-extension.json` (id, name, version, main, permissions) + `index.js` (`activate(api)` / `deactivate()`).
Render hook, cursor efekti, ayar paneli, çerçeve/duvar kağıdı/cursor paketleri eklenebilir.
Marketplace: https://marketplace.recordly.dev/extensions

## Kurulum
- Hazır sürüm: https://github.com/webadderallorg/Recordly/releases (Arch: `yay -S recordly-bin`)
- Kaynaktan:
  ```bash
  # Linux önkoşul: sudo apt install build-essential cmake libx11-dev libxtst-dev libxrandr-dev libxt-dev
  git clone https://github.com/webadderallorg/Recordly.git recordly
  cd recordly && npm install && npm run dev
  ```
  Paket: `npm run build` (veya `build:mac` / `build:win` / `build:linux`).
- Kontroller: `npm run lint`, `npm run typecheck`, `npm test`.

## GameFab için olası kullanım
- Oyun tanıtım/demo videoları ve GIF'leri çekmek (zoom + cursor efektleri + altyazı).
- Bir Recordly eklentisiyle GameFab'e özel çerçeve/filigran eklemek (eklenti kendi lisansını seçebilir).
- Kodunu GameFab'e almak lisans açısından AGPL zorunluluğu getirir — tercihen uygulamayı ayrı araç olarak kullan.
