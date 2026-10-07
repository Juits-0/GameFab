# FreeLLMAPI — inceleme notları

Kaynak: https://github.com/tashfeenahmed/freellmapi (incelenen commit `ffef850`, 7 Ekim 2026, v0.13.6)

## Nedir?
Birçok yapay zeka sağlayıcısının **ücretsiz katmanlarını** (Google, Groq, Cerebras, Mistral,
OpenRouter, Cloudflare, Cohere, NVIDIA, HuggingFace, Z.ai ... toplam ~34 sağlayıcı) tek bir
**OpenAI uyumlu `/v1` API** arkasında birleştiren, kendi bilgisayarında/sunucunda çalışan bir router.
- Her sağlayıcı için kendi ücretsiz API anahtarını eklersin; anahtarlar SQLite'ta AES-256-GCM ile şifreli durur.
- Dışarıya tek bir `freellmapi-…` anahtarı verir.
- Bir sağlayıcı limit verince (429/5xx) otomatik olarak sıradakine geçer, her anahtarın RPM/RPD/TPM limitlerini takip eder.
- Model kataloğunu freellmapi.co'dan imzalı feed ile çeker (ücretsiz kurulum aylık snapshot, premium $19/yıl aynı gün).

## Lisans
**MIT** — serbestçe kullanılabilir, kopyalanabilir, ticari kullanım dahil. Sadece telif notu korunmalı.

## Desteklenen uç noktalar
`/v1/chat/completions`, `/v1/responses`, `/v1/completions`, `/v1/embeddings`, `/v1/images/generations`,
`/v1/videos/generations`, `/v1/audio/speech`, `/v1/audio/transcriptions`, `/v1/models`,
Anthropic formatı `/v1/messages`, Gemini `/v1beta`, isteğe bağlı Ollama emülasyonu, `/mcp` MCP sunucusu.
Ekstralar: tool calling, structured output, "fusion" (birden çok modele sorup hakem modelle birleştirme),
profil bazlı fallback zincirleri (`auto:coding` gibi), prompt sıkıştırma, cache, analitik paneli.

## Teknoloji
Node 20+, TypeScript. `server/` (API + router), `client/` (React dashboard), `desktop/` (Electron tray uygulaması),
`cli/` (`npx freellmapi ...`), `shared/`. Docker imajı: `ghcr.io/tashfeenahmed/freellmapi`.

## Kurulum
- Masaüstü: Releases'tan macOS `.dmg` / Windows `.exe` (hesap gerekmez, anahtar tray menüsünde).
- Docker tek satır: `curl -fsSL https://freellmapi.co/install.sh | bash` (önce betiği okumak önerilir).
- Kaynaktan: `npm install && npm run dev` → API :3001, panel :5173.
- Panel: http://localhost:3001 → Keys sayfasına sağlayıcı anahtarlarını ekle → unified key'i al.

## Claude Code ile kullanım — DİKKAT
`npx freellmapi setup-claude --url http://localhost:3001 --api-key <unified-key>` (veya `npx freellmapi launch`)
Claude Code'u bu router'a bağlar. **Bu durumda cevapları Claude değil, ücretsiz başka modeller verir.**
README'nin kendi uyarısı: öncü (frontier) modeller yok, gecikme değişken, SLA yok; gün içinde iyi modellerin
günlük limitleri dolunca kalite düşer, UTC gece yarısı sıfırlanır.
- Bu bulut oturumunda (claude.ai/code) işe yaramaz: router senin bilgisayarında `localhost` üzerinde çalışır.
- Sağlayıcıların kullanım şartlarına uy: aynı sağlayıcıda limit aşmak için çoklu hesap açmak çoğu yerde yasak.

## GameFab için olası kullanım
- Geliştirme/prototip aşamasında ucuz LLM erişimi: NPC diyaloğu, görev/metin üretimi, test verisi, embedding.
- OpenAI uyumlu olduğu için koddaki `baseURL`'i değiştirmek yeterli; ileride ücretli sağlayıcıya geçiş kolay.
- Oyuncuya açık canlı özellik için uygun değil: SLA yok, limitler dolunca kalite/hız düşer.
- Unified key'i asla oyun istemcisine gömme; istekler kendi sunucun üzerinden geçmeli.
