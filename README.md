# Plutus Alwaysucceed

Đây là mẫu hợp đồng thông minh Plutus cơ bản. Mục tiêu là cung cấp sự thể hiện tối thiểu của một dự án Plutus được sử dụng làm điểm  để xây dựng các hợp đồng phức tạp hơn.

## Môi trường phát triển

Để xây dựng tập lệnh, bạn sẽ cần chuỗi công cụ Haskell (GCH, Cabal, v.v.) và một số phần phụ thuộc từ kho lưu trữ IOHK. Ngoài ra còn có một yêu cầu đối với thư viện [secp256k1](https://github.com/bitcoin-core/secp256k1.git). Sau khi biên dịch mã nguồn thành tập lệnh Plutus, bạn sẽ cần Node Cardano được đồng bộ hóa hoàn toàn và tệp nhị phân `cardano-cli` để gửi các giao dịch tới Blockchain.

**Nếu không muốn tự cài đặt các thành phần cần thiết**, bạn có thể sử dụng nền tảng [Demeter.run](https://demeter.run) để tạo môi trường đám mây có quyền truy cập vào cơ sở hạ tầng Cardano chung. Lệnh sau sẽ mở kho lưu trữ này trong một IDE VSCode dựa trên web, riêng tư với tất cả chuỗi công cụ Haskell cần thiết, quyền truy cập vào Node Cardano được chia sẻ và tệp nhị phân được cài đặt sẵn của `cardano-cli`.

[![Mã trong Cardano Workspace](https://demeter.run/code/badge.svg)](https://demeter.run/code?repository=https://demeter.run/code/?repository=https://github.com/cardano2vn/plutus-alwaysucceed.git&template=plutus)

Bạn cũng có thể git template này về máy tính của bạn 

```sh
git clone https://github.com/cardano2vn/plutus-alwaysucceed.git
```

## I. Bắt đầu 

> **Lưu ý**
> Hướng dẫn này giả định rằng bạn đang sử dụng Cardano Workspace như chi tiết ở trên.

### 1. Biên dịch Trình xác thực

Mã nguồn của hợp đồng Plutus nằm trong thư mục `src/Alwaysucceed`. `Contracts.hs` chứa logic trình xác thực tối giản và mã soạn sẵn được biên dịch thành Plutus-Tx dưới dạng UPLC có thể được gửi trên chuỗi.

Sử dụng lệnh cabal với mã nằm trong `Main.hs` và có thể được sử dụng để kích hoạt quá trình biên dịch này. Chạy lệnh sau từ Terminal:

```sh
cabal run plutus-alwaysucceed-kit -- assets/contract.plutus
```

> **Lưu ý**
> _Cardano Workspace_ cung cấp phiên bản được lưu trong bộ nhớ đệm của các phần phụ thuộc api Cardano. Điều này tăng tốc đáng kể quá trình xây dựng.

### 2. Mã CborHex của trình xác thực 

Khi lệnh kết thúc, bạn sẽ nhận được tệp JSON `assets/contract.plutus` chứa mã UPLC. Tập tin này có thể được sử dụng để gửi các giao dịch trên chuỗi.



```json
{
    "type": "PlutusScriptV2",
    "description": "",
    "cborHex": "5907c35907c001000..."
}
```
### 3. Tạo địa chỉ của trình xác thực

Để xây dựng các giao dịch trên chuỗi, chúng ta cần địa chỉ của tập lệnh mà chúng ta vừa biên dịch. Để làm điều này, hãy chạy lệnh sau:

```sh
cardano-cli address build \
  --payment-script-file ./assets/contract.plutus \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --out-file ./assets/contract.addr
```

> **Lưu ý**
> Biến env `CARDANO_NODE_MAGIC` được Cardano Workspace đặt tự động.

### 4. Kiểm tra eUTxO trong trình xác thực

Nếu bạn muốn truy vấn số dư địa chỉ tập lệnh của mình, chúng tôi đã thêm tập lệnh trợ giúp truy vấn UTxO cho địa chỉ được tạo ở bước trước. Mở Terminal Cardano Workspace của bạn và chạy lệnh sau:

```sh
./scripts/contract-balance.sh
```

> **Lưu ý**
> Truy vấn Node cho UTxO không nằm trong phạm vi của Bộ công cụ này. Nếu bạn muốn tìm hiểu thêm về các hoạt động phổ biến thông qua CLI, hãy dùng thử [Bộ công cụ  Cardano-CLI](https://github.com/txpipe/cardano-cli-starter-kit)

### 5. Chuẩn bị ví Dev

Để tương tác với hợp đồng trực tuyến, chúng ta sẽ cần một ví có một số tAda (ADA thử nghiệm). Mở Terminal Cardano Workspace của bạn và thực hiện lệnh sau:

```sh
./scripts/new-dev-wallet.sh
```

> **Lưu ý**
> Tạo khóa ví không nằm trong phạm vi của Bộ công cụ này. Nếu bạn muốn tìm hiểu thêm về các hoạt động phổ biến thông qua CLI, hãy dùng thử [Bộ công cụ  Cardano-CLI](https://github.com/txpipe/cardano-cli-starter-kit)

Khi hoàn tất, bạn sẽ thấy các tệp mới bên trong thư mục `asset` chứa các cặp khóa và địa chỉ cho ví mới tạo của bạn.

Nếu bạn muốn truy vấn số dư địa chỉ ví của mình, bạn có thể sử dụng tập lệnh trợ giúp truy vấn UTxO cho địa chỉ được tạo ở bước trước. Mở terminal Cardano Workspace của bạn và chạy lệnh sau:

```sh
./scripts/dev-wallet-balance.sh
```

> **Lưu ý**
> Truy vấn Node cho UTxO không nằm trong phạm vi của Bộ công cụ này. Nếu bạn muốn tìm hiểu thêm về các hoạt động phổ biến thông qua CLI, hãy dùng thử [Bộ công cụ Cardano-CLI](https://github.com/txpipe/cardano-cli-starter-kit)

Ví của bạn, chúng tôi sẽ cần một số tiền để tương tác với hợp đồng. Bạn có thể sử dụng [faucet](https://docs.cardano.org/cardano-testnet/tools/faucet) tương ứng để nhận một số tADA.


## II. Khóa tiền

Bây giờ chúng tôi đã có sẵn tập lệnh xác thực, chúng tôi sẽ khóa tiền vào địa chỉ tập lệnh. Khóa tiền chỉ là một cách nói hoa mỹ rằng chúng tôi sẽ gửi một số tADA (trong trường hợp này) tới tập lệnh bằng cách gửi giao dịch đến địa chỉ tương ứng. Nó được gọi là "khóa" vì tiền chỉ có thể được lấy nếu tập lệnh trình xác thực cho phép.

Trình xác nhận này rất cơ bản, nó luôn luôn đúng như tên của nó "alwaysucceed" có nghĩa là bất kỳ ai cũng có thể sử dụng UTXO trên đia chỉ Hợp đồng thông minh này.

### 1. Tạo mã hash cho Datum

**Khi khóa tiền**

Ví dụ ta có thể chuyển vào Datum một giá trị bất kỳ ở đây ta dùng số nguyên 42 và chuyển lên hàm băm của giá trị đó. Để làm được điều nay, chúng ta cần một biểu diễn JSON của Datum để cardano-cli chuyển nó thành hàm băm. Tệp `assets/lock.datum` chứa một ví dụ về biểu diễn JSON cho một Datum chứa giá trị `42`:

```json
{"constructor":0,"fields":[{"int":42}]}
```

Từ bên trong Cardano Workspace của bạn, hãy mở một Terminal và thực hiện lệnh sau để tạo hàm băm cho dữ liệu có trong tệp `assets/lock.datum`. Kết quả của lệnh cardano-cli sẽ được lưu trữ trong biến `scriptdatumhash`.


```sh
scriptdatumhash=$(cardano-cli transaction hash-script-data --script-data-file assets/lock.datum)
```
### 2. Kiểm tra các UTxO trong ví Dev 

Giao dịch khóa cần tham chiếu đến UTxO trong ví Dev của bạn để được sử dụng làm nguồn cho số tiền mà chúng ta sẽ khóa trong tập lệnh. Vì bước này dành riêng cho trạng thái ví của bạn nên bạn sẽ cần chỉ định giá trị trong biến shell theo cách thủ công để bước tiếp theo thành công.

Sử dụng `dev-wallet-balance.sh` để kiểm tra UTxO có sẵn của bạn và chọn một UTxO sẽ được sử dụng trong giao dịch thử nghiệm của chúng tôi. Gán `{TxHash}#{TxIn}` cho biến shell `locktxin`. Chúng tôi sẽ khóa một lượng nhỏ tADA (1230000 lovelace), hãy đảm bảo rằng UTxO của bạn có đủ. Sau đây chỉ là một ví dụ, thay thế cho phù hợp:

```sh
$ ./scripts/dev-wallet-balance.sh

                           TxHash                                 TxIx   Amount
---------------------------------------------------------------------------------
0939be18d8583bbdd7309b4cfefd419c8900df0f84142149066ec2755c94a322     0   9980637126 lovelace
9805cc2d7c08f8b99acd2d60d9cf1e3eb14b281e7f3f430f26a26f0927ff5fde     0   1060942 lovelace
9ec2a9a546d8a9c7221be452e26278d2128cb39429d57a58b420598c0e9c2591     0   1060678 lovelace

$ locktxin=0939be18d8583bbdd7309b4cfefd419c8900df0f84142149066ec2755c94a322#0
```

### 3. Download Tham số giao thức

Chúng ta cũng cần truy xuất một số Tham số giao thức trước khi xây dựng giao dịch, để thực hiện điều đó, hãy thực thi trình trợ giúp tập lệnh sau:

```sh
$ ./scripts/download-params.sh
```

### 4. Xây dựng giao dịch khóa tài sản

Bây giờ chúng ta cuối cùng đã sẵn sàng để xây dựng giao dịch khóa. Từ bên trong Cardano Workspace của bạn, hãy mở một Terminal và thực hiện lệnh sau để tạo lock.Tx chưa được ký.

```sh
cardano-cli transaction build \
  --babbage-era \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --change-address $(cat assets/wallet1.addr) \
  --tx-in ${locktxin} \
  --tx-out $(cat assets/contract.addr)+1230000 \
  --tx-out-datum-hash ${scriptdatumhash} \
  --protocol-params-file assets/params.json \
  --out-file assets/lock.tx
```

> **Lưu ý**
> Biến env `CARDANO_NODE_MAGIC` được Cardano Workspace đặt tự động.

### 5.Ký giao dịch

Bước tiếp theo bao gồm ký giao dịch bằng khóa ví Dev của chúng ta. Từ bên trong Cardano Workspace của bạn, hãy mở một Terminal và thực hiện lệnh sau:

```sh
cardano-cli transaction sign \
  --tx-body-file assets/lock.tx \
  --signing-key-file assets/wallet1.skey \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --out-file assets/lock.tx-signed
```
### 6. Submit giao dịch

Nhiệm vụ duy nhất còn lại là gửi giao dịch đã ký trên chuỗi. Từ bên trong  Cardano Workspace của bạn, hãy mở một Terminal và thực hiện lệnh sau:

```sh
cardano-cli transaction submit \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --tx-file assets/lock.tx-signed
```

### 7. Kiểm tra eUTxO trên Hợp đồng thông minh

Sau vài giây (có thể lâu hơn tùy thuộc vào hoạt động của chuỗi), số dư của địa chỉ tập lệnh sẽ hiển thị số tiền bị khóa của chúng tôi. Kiểm tra UTxO của địa chỉ tập lệnh bằng tập lệnh trợ giúp của chúng tôi:

```sh
./scripts/contract-balance.sh
```

Đầu ra của tập lệnh sẽ hiển thị nội dung tương tự như sau:

```sh
 TxHash    TxIx        Amount
--------------------------------------------------------------------------------------
b00...313     1        1230000 lovelace + TxOutDatumHash ScriptDataInBabbageEra "923...4ec"
```

## III. Mở khóa tiền

Để mở khóa Tài sản (tADA), chúng ta cần chuẩn bị một giao dịch sử dụng UTxO từ địa chỉ hợp đồng và bao gồm cả `Redeemer` và tuân thủ các giới hạn do trình xác thực của chúng ta xác định. Với hợp đồng luôn luôn đúng này thì cho bất kỳ giá trị `Redeemer` nào vào cũng được.

### 1. Kiểm tra eUTxO trên Hợp đồng thông minh

Truy vấn số dư hợp đồng một lần nữa, lấy TxHash và TxIx và lưu trữ nó bên trong biến `lockedtxin`.

```sh
$ ./scripts/contract-balance.sh
 TxHash    TxIx        Amount
--------------------------------------------------------------------------------------
8af...ee4     0        1230000 lovelace + TxOutDatumHash ScriptDataInBabbageEra "923...4ec"

$ lockedtxin=8afb82a260fc3c0fd4e5828da171b4ae52144669c2ec915df846cff6a628dee4#0
```

### 2. Kiểm tra UTxO của ví sử dụng Unlock tài sản

Chúng ta cũng cần chỉ định một số UTxO thế chấp mà giao thức có thể sử dụng trong trường hợp trình xác thực của chúng ta không thành công. Để thực hiện việc này, hãy truy vấn lại số dư ví của bạn, chọn một UTxO có sẵn và lưu trữ nó trong biến `collateraltxin`.

```sh
$ ./scripts/dev-wallet-balance.sh

                           TxHash                                 TxIx   Amount
---------------------------------------------------------------------------------
0939be18d8583bbdd7309b4cfefd419c8900df0f84142149066ec2755c94a322     0   9980637126 lovelace
9805cc2d7c08f8b99acd2d60d9cf1e3eb14b281e7f3f430f26a26f0927ff5fde     0   1060942 lovelace
9ec2a9a546d8a9c7221be452e26278d2128cb39429d57a58b420598c0e9c2591     0   1060678 lovelace

$ collateraltxin=0939be18d8583bbdd7309b4cfefd419c8900df0f84142149066ec2755c94a322#0
```

### 3. Tạo giao dịch Unlock tài sản

```sh
cardano-cli transaction build \
  --babbage-era \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --tx-in ${lockedtxin} \
  --tx-in-script-file assets/contract.plutus \
  --tx-in-datum-file assets/lock.datum \
  --tx-in-redeemer-file assets/unlock.redeemer \
  --change-address $(cat assets/wallet1.addr) \
  --tx-in-collateral ${collateraltxin} \
  --protocol-params-file assets/params.json \
  --out-file assets/unlock.tx
```

### 4. Ký giao dịch Unlock tài sản

Như thường lệ, bước tiếp theo bao gồm ký giao dịch bằng khóa ví dev của chúng ta. Từ bên trong Cardano Workspace của bạn, hãy mở một thiết bị đầu cuối và thực hiện lệnh sau:

```sh
cardano-cli transaction sign \
  --tx-body-file assets/unlock.tx \
  --signing-key-file assets/wallet1.skey \
  --testnet-magic ${CARDANO_NODE_MAGIC} \
  --out-file assets/unlock.tx-signed
```

### 5. Submit giao dịch

Bây giờ chúng ta cần gửi giao dịch đã ký trên chuỗi. Từ bên trong Không gian làm việc Cardano của bạn, hãy mở một thiết bị đầu cuối và thực hiện lệnh sau:

```sh
cardano-cli transaction submit --testnet-magic ${CARDANO_NODE_MAGIC} --tx-file assets/unlock.tx-signed
```

### 6. Kiểm tra Tài sản đã về ví chưa?

Sau vài giây, giả sử rằng quá trình gửi thành công, bạn sẽ thấy nội dung đã được mở khóa trở lại trong ví nhà phát triển của mình. Sử dụng lệnh sau để kiểm tra số dư ví của bạn. Bạn sẽ thấy UTxO mới.

```sh
./scripts/dev-wallet-balance.sh
```
