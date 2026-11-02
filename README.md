# TA5YY • BC95XLT Web 1.1

**[▶ Uygulamayı aç / Open the app](https://ta5yy.github.io/bc95xlt-web/)**

**TA5YY**

Uniden BC95XLT alıcısının 200 kanalını USB/seri kablo üzerinden tarayıcıdan okuyup düzenlemek ve cihaza yazmak için Türkçe / İngilizce uygulama. Python veya ARC95 kurulumu gerekmez.

Buildless, bilingual **Turkish / English** browser programmer for the **Uniden BC95XLT**. No Python, npm, backend, CDN or external dependencies are needed to use it. Desktop Chrome/Edge and an HTTPS origin are required for Web Serial; the USB/serial cable still needs its normal driver.

## Türkçe kullanım

1. **[Uygulamayı aç](https://ta5yy.github.io/bc95xlt-web/)**. Bilgisayarda Chrome veya Edge kullan; kablonun sürücüsü kurulu olsun.
2. Alıcıyı aç, kabloyu bağla. Aynı portu kullanan ARC95 veya diğer programları kapat.
3. **Cihazı bağla** düğmesine basıp kablonun seri portunu seç.
4. **200 kanalı oku** ile mevcut hafızayı oku ve CSV yedeğini indir.
5. Satıra çift tıklayarak düzenle; sağ tık veya üç nokta ile taşı, kopyala, yer değiştir ya da boşalt. Kendi CSV dosyanı da açabilirsin.
6. **Cihaza yükle** ile aktarımı başlat. Yazmadan önce cihazın yedeği alınır; yedeğini indirip yazmayı onayla.

Okuma/yazma yüzdeli ilerleme çubuğunda izlenir. Başarıyla bitince tamamlandı penceresi açılır. Yazmada ilk %50 yedek okumadır; onay beklerken burada durabilir. Her yazılan kanal geri okunup doğrulanır.

### CSV sütunları

| Sütun | Anlamı |
| --- | --- |
| kanal | 1–200 arasındaki hafıza konumu |
| banka | Kanal numarasından hesaplanan banka; her bankada 20 kanal |
| frekans_mhz | MHz cinsinden frekans; altı ondalık basamak |
| kilit (E/H) | E: taramada bu kanalı atla. H: atlama kilidi kapalı |
| oncelik (E/H) | Cihazdan okunan öncelik; uygulamadan değiştirilmez |
| gecikme (E/H) | E: yayın bittikten sonra 2 saniye bekle |
| not | Yalnızca bilgisayarda tutulan açıklama |
| sil (E/H) | E: yüklemede bu kanalı boşalt; frekans boş veya 0 olmalı |

**E = Evet, H = Hayır.** Eski S/R dosyaları da açılır. Boş/0 frekans ve sil=H olan satır cihaza yazılmaz. Dil değişse de CSV başlıkları ve E/H aynı kalır.

Liste ve son cihaz yedeği tarayıcıda tutulur. Tarayıcı verilerinin silinmesine karşı CSV kopyalarını bilgisayarına indir. Her yeni cihaz yedeği önceki son yedeğin yerini alır. Durdurma veya hata, daha önce yazılmış kanalları geri almaz.

## English usage

1. Open **[the app](https://ta5yy.github.io/bc95xlt-web/)** in desktop Chrome or Edge. Install your USB/serial cable driver if needed.
2. Connect and switch on the receiver. Close other programs using its serial port.
3. Choose **Connect receiver**, select the port, then **Read 200 channels**.
4. Download a CSV backup. Double-click a row to edit, or use the context menu to move, copy, swap or clear channels. Existing CSV files can also be opened.
5. Choose **Write to receiver**. The app reads a fresh backup before writing; download it and confirm the write.

The progress bar shows the percentage completed. A dialog confirms successful completion. During writes, the first 50% covers the backup read; progress may pause there while waiting for confirmation. Each written channel is read back and checked.

## Features / Özellikler

- 200 channels, 10 banks; frequency/note search and bank filter.
- Turkish / English UI, dialogs, help and application error messages.
- Visible language selector, percentage read/write progress and completion dialog.
- Edit, clear, move, copy, swap and 30-step list undo.
- Compatible with desktop E/H and legacy S/R CSV files; six decimal frequencies.
- Priority remains **read only**. The receiver's existing priority stays at its channel position.
- Channel-step validation; integer-Hz representation and the real BC95XLT `446.0063 → 446.0062` protocol alias are handled.
- Before writes, all 200 receiver channels are read and saved in IndexedDB; failure prevents writing.
- A separate backup-download and write-confirmation step precedes actual writes.
- Each written channel is read back and verified. Filled destinations precede pending clears.
- Cancellation/error stops further writes but **does not roll back earlier writes**.
- Receiver mode is exited in cleanup; uncertain transport errors require reconnection.

## CSV format

Exports always retain Turkish headers and E/H (E=Yes, H=No) to stay compatible with desktop 1.4 regardless of UI language:

```text
kanal;banka;frekans_mhz;kilit (E/H);oncelik (E/H);gecikme (E/H);not;sil (E/H)
```

CSV uses UTF-8 BOM, semicolon separator and decimal commas. Quoted multiline notes are supported. English headers (`channel, bank, frequency_mhz, lock, priority, delay, note, clear`) and Y/N may also be imported. Bank is calculated from channel; editing the bank column does not move a channel.

Empty/zero rows are skipped unless explicitly marked `sil=E`. Clear requires an empty/zero frequency. Lock E skips scanning, delay E enables the 2-second delay, and notes stay on the PC.

## Storage and privacy

No analytics or network upload calls. Scripts/fonts/assets are served locally from the website. Working lists are kept in localStorage and the **latest single receiver backup** in IndexedDB; every new receiver read/write backup replaces that latest record. Keep downloaded CSV copies. Browser data removal can erase local records. GitHub still receives normal website requests; this is not anonymous hosting.

A backup with zero frequencies and `sil=H` does not clear those positions during restoration. Set `sil=E` when explicit erasing is needed. Device backups do not contain PC-only notes.

## Verification

The user confirmed that web version 1.0 reads and writes their physical BC95XLT. Version 1.1 adds UI improvements tested in the browser and automated operation tests; those new changes have not yet been tested on hardware. Tests cover all 200 channels, CSV round trips, the real C090 reply, priority-preserving transfers, read/write verification, cancellation, serial CR framing, echoed/fragmented responses and port cleanup.

To run the included developer tests (optional; not needed by end users):

```sh
node --test tests/*.cjs
```

Sources: [Uniden protocol](https://www.uniden.info/download/files/BC95XLT_Protocol.pdf), [Web Serial](https://developer.chrome.com/docs/capabilities/serial), [GitHub Pages](https://docs.github.com/en/pages/quickstart).
