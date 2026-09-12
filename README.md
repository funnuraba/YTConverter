# YTConverter Portable ユーザーマニュアル

## 1. YTConverterとは

YTConverterは、YouTubeなどの対応メディアURLから動画・音声の情報を取得し、ローカルへ保存・変換するWindowsアプリケーションです。GUIを中心に、コマンドライン版とローカルstdio MCP版も同梱しています。

他者の著作権、利用規約、地域の法令を守り、保存する権利のあるメディアにのみ使用してください。

## 2. 起動方法

`YTConverter.exe` をダブルクリックしてください。インストールや管理者権限は通常不要です。Windowsの警告が表示された場合は、配布元とファイルを確認してから実行してください。

このフォルダ内のファイルやフォルダの位置関係は変更しないでください。CLIやMCPを使う場合も、各実行ファイルをこのフォルダに置いたまま使用します。

この配布物は64-bit Windows用です。GUI表示にはMicrosoft Edge WebView2 Runtimeを使用します。現在のWindows 10/11では通常導入済みですが、見つからない場合はWindowsの案内に従ってMicrosoft提供のRuntimeを導入してください。Go、yt-dlp、FFmpegを別途手動導入する必要はありません。

## 3. Portable版について

- Windowsへのインストール、PATH設定、Goの導入は不要です。
- yt-dlp、FFmpeg、ffprobeは初回セットアップでこのフォルダ内へ取得できます。
- DB、設定、キャッシュ、managed dependency、既定のダウンロード先はすべてこのフォルダ内です。
- アプリ固有データも含めて削除するには、YTConverterを終了してから `YouTubeConverter` フォルダ全体を削除します。
- 別の保存先を選んだ動画・音声は、Portableフォルダを削除しても残ります。

`portable.flag` がPortable Modeの目印です。削除しないでください。

## 4. 初回起動とDependency Setup

初回起動時にyt-dlp、FFmpeg、ffprobeが見つからない場合、「必要なコンポーネント」画面が表示されます。

1. インターネットへ接続します。
2. 「セットアップ」を押します。
3. 3コンポーネントが「インストール済み」になるまで待ちます。
4. Downloadボタンが有効になったことを確認します。

取得したファイルは `tools` 以下だけに保存されます。Windows全体へのインストールやPATH変更は行いません。セットアップ中にアプリを終了した場合は、再起動してもう一度セットアップしてください。既存の正常なdependencyを先に削除しない安全な置換方式を使用します。

## 5. GUIの画面

### Download

URLを入力して「情報を取得」を押すと、タイトル、チャンネル、長さなどを確認できます。設定と保存先を選び、「Download」で取得・変換します。

### Basic Settings

日常的な変換向けの簡単な設定です。

- Type: VideoまたはAudioを選びます。
- Format: VideoではMP4、MKV、WebM、AudioではMP3、WAVを選びます。
- Resolution: `best` または希望する最大解像度を選びます。入手できる範囲で近い低い解像度が選ばれます。
- Quality: Fastは速度優先、Balancedは標準、Highは画質優先です。処理時間やファイルサイズも変化します。

Basic Videoの標準構成は、MP4/MKVがH.264 + AAC、WebMがVP9 + Opusです。BasicとAdvancedは排他的で、非表示側の値は処理に混入しません。

### Advanced Settings

コンテナ、コーデック、encoder、画質制御を個別に指定したい場合に使用します。

- Type: Video / Audioの処理種別です。
- Container / Format: MP4、MKV、WebMなど、映像・音声を格納するファイルの外側の形式です。
- Resolution / FPS: 出力元として選ぶ最大解像度とフレームレートです。`best` は利用可能な最良候補です。
- Video Codec: H.264、HEVC、AV1、VP9など、映像を圧縮する方式です。
- Audio Codec: AAC、Opus、MP3、FLAC、PCMなど、音声を圧縮・格納する方式です。
- Encoder Type: Auto、Software、Hardwareの分類です。
- Auto Policy: 自動選択の方針です。
- Encoder: 実際に使用するFFmpeg encoderです。`auto` では環境と負荷に応じて選択します。
- Rate Control: `quality` は品質値を基準にし、`bitrate` はデータ量を基準にします。
- Quality: 小さい値ほど高画質になるencoderが多いですが、意味はencoderによって異なります。
- Video Bitrate / Audio Bitrate: `8M`、`2500k`、`192k` のように指定します。
- Max Bitrate / Buffer Size: bitrate変動の上限と制御用bufferです。必要な場合だけ設定します。
- Preset: 速度と圧縮効率の配分です。通常はmediumまたはautoで構いません。
- Pixel Format: `yuv420p` は互換性重視、`yuv420p10le` は対応codecで10-bit処理に使います。
- Copy: 互換性がある場合に再encodeせずstreamをコピーします。高速ですが、指定した変換が必要な場合は使えません。

コンテナとコーデックは別の概念です。主な対応関係は次の通りです。

| Container | Video Codec | Audio Codec |
|---|---|---|
| MP4 | copy / H.264 / HEVC / AV1 | copy / AAC |
| MKV | copy / H.264 / HEVC / AV1 / VP9 | copy / AAC / Opus / MP3 / FLAC / PCM |
| WebM | copy / AV1 / VP9 | copy / Opus |
| MP3 | なし | MP3 |
| WAV | なし | PCM 16-bit |

プレイヤーや機器が新しいcodecへ対応していない場合は、互換性の高いMP4 + H.264 + AACを選んでください。

## 6. Auto Encoder

- Auto: CPU能力、codec、解像度、FPS、実際に利用可能なhardware encoderを考慮して選びます。常にGPUを選ぶわけではありません。
- Software: CPU上で動くencoderだけを使用します。一般に画質・圧縮効率を重視しやすい一方、処理時間が長くなる場合があります。
- Hardware: GPU等のhardware encoderだけを使用します。高速になりやすい一方、対応GPUとdriverが必要です。
- balanced: 画質と速度を総合して選びます。
- quality: Software寄りの選択です。
- speed: 利用可能ならHardware寄りの選択です。

選択後はEncoder名とSelection Reason（選択理由）が表示されます。Encoder TypeやEncoder名を明示した場合、その指定がAuto Policyより優先されます。

## 7. Hardware Encoding

FFmpegにencoder名が登録されているだけでは、実際のGPUやdriverで動作できるとは限りません。YTConverterは短時間のruntime probeを行い、実際に利用可能と確認できたhardware encoderだけを自動選択候補にします。AMD AMF、NVIDIA NVENC、Intel QSVなどの利用可否はPC、GPU、driver、FFmpeg buildによって異なります。

完全自動指定で選ばれたhardware encoderが実encode開始時に初期化できなかった場合は、安全なsoftware encoderへ最大1回fallbackすることがあります。Encoder TypeまたはEncoderを明示した場合は別encoderへfallbackせず、エラーを返します。

## 8. ProgressとCancel

画面には現在の段階、表示用進捗率、encoder情報、経過状況が表示されます。表示用進捗は、yt-dlpのDownload進捗とFFmpegのEncoding進捗を工程全体へ割り当てた、見やすさのための単調増加値です。各ツールが報告する生の進捗率と常に同じではありません。

「Cancel」または処理中のEscapeでキャンセルを要求できます。外部processの停止と一時ファイルの片付けが確認されてからキャンセル完了になります。

## 9. Library / History

成功・失敗を含むDownload履歴を表示します。日時、タイトル、出力形式、解像度、codec、状態、保存先を確認できます。履歴の正はPortableフォルダ内のSQLite DBです。

## 10. Jobs / Queue、Retry

Jobの状態、試行回数、エラーを表示します。

- Pending: 「Run」で実行できます。
- Failed / Cancelled: 「Retry」で再試行できます。
- Running: 「Cancel」で停止を要求できます。

アプリを閉じたまま常駐処理を行うbackground serviceは、現在のPortable packageにはありません。

## 11. Sources

手動URL、YouTube Channel、YouTube PlaylistのSource情報を登録・編集・有効化・無効化・削除できます。現在の画面はSource管理用であり、常駐監視や自動巡回を開始する機能ではありません。

## 12. Diagnostics

OS、Architecture、CPU core数、yt-dlp / FFmpeg / ffprobeの場所・version・source、software encoder、FFmpeg登録済みhardware encoder、runtime利用可能hardware encoder、代表条件でのAuto選択例を確認できます。問題報告時は、秘密情報や個人の保存先を除いてこの内容を添えると調査に役立ちます。

Diagnostics画面にはDependency状態とyt-dlp Update操作もあります。

## 13. yt-dlp Update

yt-dlpは対象サービス側の変更へ追従するため、更新が必要になることがあります。

- DefaultはManualです。初期状態では起動時やbackgroundでUpdate Checkを行いません。
- 「Check for updates」で、ユーザー操作による確認を行います。
- 新版がある場合は「Update yt-dlp」で更新します。
- Update ModeをAutomaticへ変更すると、約24時間の間隔で起動時に確認し、安全なタイミングで更新します。
- Automaticを選んでも手動のCheckボタンは利用できます。
- Download中は実行ファイルを置換しません。
- 更新失敗時は既存の正常なversionを維持します。
- 古いversionへのdowngradeは行いません。

以前は取得できていたURLが突然失敗するようになった場合、まずDiagnosticsで「Check for updates」を試してください。Automatic Updateはdefaultではありません。

## 14. 対応言語

日本語、English、Deutsch、Italianoに対応しています。画面上部のLanguage選択で切り替えられ、選択内容は同じPC上のGUI設定として保持されます。Container、Codec、Encoder名などの技術的な内部値は翻訳されません。

## 15. Output Directoryと保存場所

保存先はDownload画面の「Browse...」から変更できます。Portable Modeの既定配置は次の通りです。

```text
YouTubeConverter\
├ YTConverter.exe
├ YTConverter-CLI.exe
├ YTConverter-MCP.exe
├ portable.flag
├ data\                 SQLite DB
├ config\               Update mode等の設定
├ tools\                yt-dlp / FFmpeg / ffprobe
├ cache\                一時的なdependency download等
└ downloads\            既定の動画・音声保存先
```

GUIで別フォルダを選んだ場合、その出力は選択先へ保存されます。

## 16. CLI

PowerShellまたはコマンドプロンプトでPortableフォルダへ移動し、`YTConverter-CLI.exe` を使用します。URLや空白を含むpathは引用符で囲んでください。Ctrl+Cで処理をキャンセルできます。

### URL変換

```text
YTConverter-CLI.exe <URL> --format <mp3|wav|mp4> [options]
```

基本例:

```text
.\YTConverter-CLI.exe "https://www.youtube.com/watch?v=..." --format mp3
.\YTConverter-CLI.exe "https://www.youtube.com/watch?v=..." --format wav -o ".\downloads"
.\YTConverter-CLI.exe "https://www.youtube.com/watch?v=..." --format mp4
```

Advanced例:

```text
.\YTConverter-CLI.exe "<URL>" --container mp4 --resolution 1080p --fps 60 --video-codec h264 --audio-codec aac
.\YTConverter-CLI.exe "<URL>" --container mkv --video-codec av1 --audio-codec opus --encoder auto --encoder-type auto --auto-policy balanced
```

`--format` と `--container` / codec指定は同時に使用できません。Advanced指定では `--container` と `--audio-codec` が必須で、動画containerでは `--video-codec` も指定します。

利用可能option:

| Option | 用途 / 値 |
|---|---|
| `--format` | 簡易出力: `mp3`, `wav`, `mp4` |
| `--container` | `mp3`, `wav`, `mp4`, `mkv`, `webm` |
| `--video-codec` | `copy`, `h264`, `hevc`（`h265`も可）, `av1`, `vp9` |
| `--audio-codec` | `copy`, `aac`, `opus`, `mp3`, `flac`, `pcm_s16le`（`pcm`, `wav`も可） |
| `--resolution` | `best`, `4320p`, `2160p`, `1440p`, `1080p`, `720p`, `480p`, `360p`, `240p`, `144p` |
| `--fps` | `best`, `120`, `60`, `50`, `30`, `25`, `24` |
| `--encoder-type` | `auto`, `software`, `hardware` |
| `--encoder` | `auto` またはFFmpegへ登録された対応encoder名 |
| `--auto-policy` | `balanced`, `quality`, `speed` |
| `--rate-control` | `quality`, `bitrate` |
| `--quality` | `0`～`63`。既定 `23` |
| `--video-bitrate` | 例: `8M`, `2500k`。bitrate modeでは必須 |
| `--audio-bitrate` | 例: `192k` |
| `--preset` | `auto`, `veryfast`, `fast`, `medium`, `slow`, `veryslow` |
| `--pixel-format` | `auto`, `yuv420p`, `yuv420p10le` |
| `-o`, `--output` | 保存先directory。CLIの既定は現在のdirectory |
| `-h`, `--help` | Help表示 |

### doctor

```text
.\YTConverter-CLI.exe doctor
```

Dependency、CPU、encoder登録状況、runtime probe、Auto選択例を表示します。

### history

```text
.\YTConverter-CLI.exe history
```

共有SQLite DBの直近100件のDownload履歴を表示します。

### jobs

```text
.\YTConverter-CLI.exe jobs
```

共有SQLite DBの直近100件のJob状態を表示します。

### sources

```text
.\YTConverter-CLI.exe sources
```

共有SQLite DBの直近100件のSource登録を表示します。

CLIにはDependency Setupやyt-dlp Update専用commandはありません。最初にGUIのFirst Run Setupを完了してください。

## 17. Local stdio MCP

`YTConverter-MCP.exe` は、MCP対応クライアントからYTConverterの共有Application機能を呼び出すためのローカルstdio serverです。HTTP、TCP、SSE、WebSocket serverではなく、portをlistenしません。標準出力はMCP protocol専用で、diagnosticsとprogressは標準エラーへ送られます。

クライアントには、引数なしで次の実行ファイルを起動するよう設定します。

```text
C:\path\to\YouTubeConverter\YTConverter-MCP.exe
```

利用可能tool:

- `get_media_info`: URLの基本情報、利用可能な解像度・FPS・codec・source formatを取得します。
- `download_audio`: `format` に `mp3` または `wav` を指定して、既定の安全な `downloads` フォルダへ保存します。
- `download_video`: MP4（H.264 + AAC）として既定の安全な `downloads` フォルダへ保存します。

MCPは起動時にdependencyが必要です。先にGUIのFirst Run Setupを完了してください。GUI、CLI、MCPは同じPortable DBとmanaged dependencyを利用します。

## 18. Troubleshooting

### Downloadできない / 以前のURLが失敗する

URLがHTTP/HTTPSで正しいか確認し、Diagnosticsでyt-dlpの「Check for updates」を試してください。配信側の制限、認証、地域制限、削除済みメディアなどは更新しても取得できない場合があります。

### FFmpegまたはffprobeが見つからない

First Run Setupを実行してください。`tools` の一部だけを手動で移動しないでください。Diagnosticsでpath、version、sourceを確認できます。

### Hardware Encoderが使用できない

GPU driverを更新し、Diagnosticsの「Runtime-usable hardware encoders」を確認してください。FFmpegに名前が登録されていても、GPUやdriverが対応しなければ使用できません。AutoまたはSoftwareを選ぶと処理できる場合があります。

### Unsupported format / codec

上の対応表にある組み合わせへ変更してください。古いプレイヤー向けにはMP4 + H.264 + AACが無難です。

### Download途中で失敗した

ネットワーク、空き容量、保存先への書き込み権限を確認し、QueueからRetryしてください。一時ファイルは可能な範囲で片付けられます。

### Portableフォルダへ書き込めない

読み取り専用媒体、保護されたsystem directory、他ユーザーだけが書き込める場所を避け、ユーザーが書き込めるフォルダへ `YouTubeConverter` 全体を移動してください。実行中の移動は避けてください。

### Cancelしてもすぐ止まらない

外部processと一時ファイルを安全に終了するまで少し時間がかかることがあります。強制終了は破損した出力を残す可能性があります。

## 19. Internet AccessとPrivacy

Internet通信が発生するのは次の場合です。

- URLのMedia Info取得
- 動画・音声のDownload
- First Run Dependency Setup
- ユーザーが押したyt-dlp Update Check / Update
- Automatic Updateを明示的に選択した後の定期check/update

Manual Updateがdefaultであり、Manual Modeでは自動Update Checkを行いません。YTConverter独自のtelemetry、利用状況送信、cloud syncは実装していません。ただし、yt-dlpによる取得先およびdependency/update提供元には通常のnetwork requestが送信されます。

## 20. Third-party software

YTConverterはyt-dlp、FFmpeg / ffprobe、Wails、SQLite関連ライブラリ、MCP Go SDKなどのthird-party softwareを利用します。詳細は同梱の `THIRD_PARTY_NOTICES.txt` と `licenses` フォルダを確認してください。

- yt-dlp: https://github.com/yt-dlp/yt-dlp
- FFmpeg: https://ffmpeg.org/
- Wails: https://wails.io/
- Model Context Protocol: https://modelcontextprotocol.io/
- SQLite: https://sqlite.org/

Dependency Setupで後から取得されるyt-dlpとFFmpegのlicense条件は、それぞれ取得された配布物および公式サイトも確認してください。FFmpegのlicense条件は使用されるbuild configurationによって異なる場合があります。

## 21. Legal Notice / 免責事項

YTConverterは非公式の独立したソフトウェアであり、
GoogleまたはYouTubeによって提供・承認・支援されているものではありません。

本ソフトウェアの利用は自己責任です。
利用者は、GoogleおよびYouTubeを含む各サービスの利用規約、
著作権その他の権利、および利用者の国・地域で適用される法律を
遵守してください。

コンテンツをダウンロード・変換できることは、
その利用が法律またはサービスの利用規約上許可されていることを
意味しません。

本ソフトウェアには動作保証およびサポート義務はありません。
詳細は [DISCLAIMER.md](DISCLAIMER.md) を参照してください。

## 22. License

YTConverter is licensed under the BSD Zero Clause License (0BSD).

You may use, modify, redistribute, and use this software commercially
without permission or attribution.
See `LICENSE` for details.