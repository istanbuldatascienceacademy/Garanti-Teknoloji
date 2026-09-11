# Copilot, MCP ve Token Ekonomisi

**Bir günlük atölye · Yazılım mühendisleri ve iş analistleri için**

> Bağlam bir bütçedir. Ne gönderdiğiniz, neyi açtığınız, ne ödediğiniz.

Modele gönderdiğiniz her şey — talimat dosyalarınız, açtığınız MCP araçları,
sohbet geçmişiniz — her istekte yeniden gönderiliyor ve her istekte yeniden
faturalanıyor. 1 Haziran 2026'daki kullanım bazlı faturalama geçişiyle bu artık
bir benzetme değil.

Bu repo, o gerçeği ölçülebilir hâle getiren bir atölyenin tüm materyallerini
içeriyor: çalışan bir MCP sunucusu, bir maliyet hesaplayıcısı, dört uygulamalı
lab ve 60 slaytlık sunum.

---

## Hızlı başlangıç

```bash
git clone <REPO-ADRESI>
cd workshop

python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
python -m pip install -r requirements.txt

python -m pytest tests -q          # 28 passed
python cost/scenarios.py           # önce/sonra maliyet tablosu
```

MCP sunucusunu bağlamak için yorumlayıcının tam yolunu yapılandırmaya yazın:

```bash
python - <<'EOF'
import json, sys, pathlib
kok = pathlib.Path.cwd()
cfg = kok / ".vscode" / "mcp.json"
cfg.parent.mkdir(exist_ok=True)
cfg.write_text(json.dumps({"servers": {"corp-internal": {
    "type": "stdio", "command": sys.executable,
    "args": [str(kok / "mcp_server" / "corp_server.py")]}}}, indent=2))
print(cfg.read_text())
EOF
```

Sonra VS Code'da klasörü açın, çalışma alanına güvenin, sohbeti **Agent** rolüne
alın ve komut paletinden **MCP: List Servers** → `corp-internal` → Start Server.

> Düz `python` yazarsanız PATH'teki ilk yorumlayıcı çalışır, `mcp` paketi orada
> olmadığı için süreç sessizce ölür ve **output kanalı boş kalır**. Tam yol
> yazın. Ayrıntı: [Lab 2, Adım 1](labs/LAB-2-mcp-sunucusu.md).

---

## Kimler için

**Yazılım mühendisleri** — Copilot'u dosyayla yönetmeyi, MCP sunucusu yazmayı ve
maliyeti ölçmeyi öğrenir.

**İş analistleri** — kendilerine özel bir sohbet modu, MCP araçlarını
değerlendirme pratiği ve bütçe sahipliği tartışması. Ayrı bir kol var ve kolay
olan kol değil: *hangi aracın yazılmaması gerektiği* sorusu, iş sonucunu bilene
ait.

**Ön koşullar:** VS Code (kararlı sürüm), Copilot koltuğu, Python 3.10+, ve
kurumsal planda **Agent modu ile MCP'ye politika izni**. Sonuncusu olmadan günün
yarısı çalışmaz.

---

## Ne öğreniliyor

| # | Modül | Lab |
|---|---|---|
| 1 | Talimat dosyası hiyerarşisi — hangi kural hangi dosyaya | [Lab 1](labs/LAB-1-talimat-dosyalari.md) · 45 dk |
| 2 | MCP mimarisi ve bankada anlamı | [Lab 2](labs/LAB-2-mcp-sunucusu.md) · 60 dk |
| 3 | Token ekonomisi ve üç eşik | [Lab 3](labs/LAB-3-olcum.md) · 45 dk |
| 4 | Yedi optimizasyon kaldıracı | [Lab 4](labs/LAB-4-optimizasyon.md) · 60 dk |

### Üç eşik

| Eşik | Kat | Ne yapılır |
|---|---|---|
| Model seçimi | **25×** | Rutin işi hafif modele yönlendir |
| Önbellek | **10×** | Bağlamın sabit kısmını sabit tut |
| Uzun bağlam (272K) | **2×** | Tüm repoyu bağlama atma |

Kaldıraçlar toplanmıyor, **çarpılıyor**. Lab 3'te bunu kendiniz göreceksiniz:
sadece model 25×, sadece bağlam 9×, ikisi birden **224×**.

---

## Repo içeriği

```
.github/
  copilot-instructions.md            her istekte yüklenir · ~290 token
  instructions/*.instructions.md     applyTo ile kapsamlanmış
  prompts/*.prompt.md                /komut ile çağrılır
  chatmodes/analyst.chatmode.md      analist modu, kısıtlı araçlar
.vscode/mcp.json                     MCP sunucu tanımı
mcp_server/corp_server.py            5 araç · kişisel veri yok
cost/
  calculator.py                      fiyat tablosu + maliyet motoru
  scenarios.py                       önce/sonra senaryo karşılaştırması
labs/                                dört lab (TR) · labs/en/ (EN)
tests/                               28 test · çevrimdışı
```

### MCP sunucusu bilerek müşteri verisi döndürmüyor

Bankaya MCP sunucusu yazarken ilk akla gelen fikir genelde yanlış olanıdır.
Müşteri 360 teknik olarak kolaydır ve kişisel veriyi, binadan çıkan bir bağlam
penceresine koyar.

Bu sunucu bunun yerine mühendislik standartlarını, dahili API kataloğunu ve sürüm
takvimini açıyor. Katalog bir servisin kişisel veri **içerdiğini söyler** —
verinin kendisini vermez. Hassas verinin nerede olduğunu tarif etmek, onu
taşımaktan farklı bir eylemdir.

Bu kural bir testle korunuyor: `test_no_tool_returns_personal_data`.

---

## Eğitmen materyalleri





## Notlar

Bu materyaldeki maliyet senaryoları **modellenmiş kullanım varsayımlarına**
dayanıyor; kanıt değil, büyüklük mertebesi göstergesi. Gerçek rakam
**Settings → Billing → Usage** ekranında.

Tahminle bütçe kurmak, ölçmemenin kibarcasıdır.

---

Zekeriya Beşiroğlu 
