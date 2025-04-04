# SBTP
## 概要
SBTP`Simple Bytes Transfer Protocol`はUARTなどのバイトベースのストリーム上でデータをフレーム化するためのプロトコルです。

### 特徴
- シンプル
- 255byteまで転送可能

## フォーマット
```
| SOF | Data Length | Payload | CRC | EOF |
```

### SOF(Start of Frame)
フレームの開始を示す1byte。`0x55`

### Data Length
`Payload`の長さを示す1byte。0〜255の間の値

### Payload
送信するデータ。
もしデータ内に`SOF`、`EOF`、またはエスケープバイトを含む場合、エスケープバイトを挿入し、そのバイトと`0x42`のXORを取ったバイトを挿入する
エスケープバイトは`0x5A`とする

### CRC
`Payload`のCRC値

### EOF(End of Frame)
フレームの終了を示す1byte。`0xAA`
