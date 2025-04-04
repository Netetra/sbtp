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

## 例
### `0x12 0x34 0x56`の3byteを送る場合
```
0x55 0x03 0x12 0x34 0x56 0x10 0xAA
```

CRCは以下のコードで計算可能
```rs
const GENERATE_POLYNOMIAL: u8 = 0xD5; // 生成多項式
const INITIAL_VALUE: u8 = 0xFF; // 初期値
const FINAL_XOR: u8 = 0xFF; // 最後のXORする値

fn crc8(datas: &[u8]) -> u8 {
  let mut crc = INITIAL_VALUE;
  for data in datas {
    crc ^= data;
    for _ in 0..8 {
      if crc & 0x80 != 0 {
        crc = (crc << 1) ^ GENERATE_POLYNOMIAL;
      }
      else {
        crc <<= 1;
      }
    }
  }
  crc ^ FINAL_XOR
}

fn main() {
  let datas = [0x12, 0x34, 0x56];
  println!("0x{:x}", crc8(&datas));
}
```

### `0x12 0x55 0x34 0xAA 0x56 0x5A`の6byteを送る場合
```
0x55 0x06 0x12 0x5A 0x17 0x34 0x5A 0xE8 0x56 0x5A 0x18 0x80 0xAA
```
