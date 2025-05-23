

# Data Cleaning Report: [Cafe Sales - Dirty Data for Cleaning Training ]

## Dataset Overview
- **Sumber Data**: https://www.kaggle.com/datasets/ahmedmohamed2003/cafe-sales-dirty-data-for-cleaning-training
- **Jumlah Data Awal**: 10000 baris, 8 kolom  
- **Tujuan Cleaning**: Melatih diri dalam membersihkan dataset kotor dan mempersiapkan 
data untuk dianalisis
---

## Langkah-Langkah Data Cleaning

### **1. Cek Distribusi Data**
#### **Kolom**:  
- `[Transaction ID]`: 100% Valid.
- `[Item]`: 95% Valid, 0% Error, 4% Empty.
- `[Quantity]`: 98% Valid, 0% Error, 2% Empty.
- `[Price Per Unit]`: 95% Valid, 0% Error, 4% Empty.
- `[Total Spent]`: 99% Valid, 0% Error, 1% Empty.
- `[Payment Method]`: 74% Valid, 0% Error, 26% Empty.
- `[Location]`: 71% Valid, 0% Error, 30% Empty.
- `[Transaction Date]`: 98% Valid, 0% Error, 2% Empty.

#### **Distribusi Data Anomali**
| **Nilai**  | **item** | **Quantity** | **Price Per Unit** | Total Spent | **Payment Method** | **Location**  | **Transaction Date** |
|:----|:------------|:--------------:|-------------:|:----------------|:-----------------:|----------------:|:---------------:|
| ERROR | 292 |170|190|164|306| 358 |142|    
| UNKNOWN| 344 |171|164|165|293| 338 |159|     
| NULL| 333 |138|179|173|2579|3265|159|      

Catatan: nilai "ERROR" bukan error sungguhan seperti data yang tidak terbaca, melainkan memang nilai yang berisi string "ERROR", begitu juga "UNKNOWN"

Sebelum anaisis, pastikan format data telah disesuaikan, seperti **Number** untuk kolom Price per Unit, Quantity, dan Total Spent, **Date** untuk Transaction Date dan sisanya **General**.

---

### **2. Cleaning (Kolom Item & Price Per Unit)**

Melihat kolom Item dan Price Per Unit, saya menemukan bahwa beberapa harga masing - masing merujuk ke satu produk yang sama, seperti:
- **Cookie = 1**
- **Coffee = 2**
- **Salad = 5**
- **Tea = 1.5**

Ada 2 harga yang masing - masing merujuk ke 2 produk berbeda, seperti :
- **Cake & Juice = 3**
- **Smoothie & Sandwich = 4**

Dengan informasi ini, saya dapat input data dengan benar, untuk mengatasi item kosong dengan data Price yang pasti, serta menangani nilai Price yang salah berdasarkan informasi Item.

#### **Aksi 1**:  
Mengisi kolom kosong, "ERROR" dan "UNKNOWN" berdasarkan harga, namun untuk harga 3 dan 4 dibiarkan dulu. Karena di excel tidak bisa mengganti isi kolom langsung, maka saya membuat kolom baru dengan nama **Input1**. Formulanya:
```excel
=IFS(AND(OR([@Item]="";[@Item]="ERROR";[@Item]="UNKNOWN");[@[Price Per Unit]]=1);"Cookie";AND(OR([@Item]="";[@Item]="ERROR";[@Item]="UNKNOWN");[@[Price Per Unit]]=2);"Coffee";AND(OR([@Item]="";[@Item]="ERROR";[@Item]="UNKNOWN");[@[Price Per Unit]]=5);"Salad";AND(OR([@Item]="";[@Item]="ERROR";[@Item]="UNKNOWN");[@[Price Per Unit]]=1.5);"Tea";TRUE;[@Item])**
```
#### **Aksi 2**: 
Dilanjutkan mengisi kolom  Price kosong, “ERROR” dan “UNKNOWN” dengan nilai yang sesuai dengan daftar Item dan Price sebelumnya. Kolom ini dinamakan **Input2**. Formulanya:
``` excel
=IFS(AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Cookie");1;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Coffee");2;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Cake");3;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Juice");3;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Smoothie");4;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Sandwich");4;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Salad");5;AND(OR([@[Price Per Unit]]="";[@[Price Per Unit]]="ERROR";[@[Price Per Unit]]="UNKNOWN");[@Input1]="Tea");1.5;TRUE;[@[Price Per Unit]])
```
#### **Aksi 3**: 
Setelah dilakukan input, ternyata di kolom Price, masih ada nilai null (menjadi 0), “ERROR” dan “UNKNOWN”. Setelah saya cari tahu, nilai - nilai tersebut belum terisi karena kolom Item yang masih nilai null (menjadi 0), “ERROR” dan “UNKNOWN”. Oleh karena itu, saya coba untuk gunakan kolom Total Spent  dan Quantity untuk mendapatkan nilai Price. Formulanya:
``` excel
=IF(OR([@Input2]=0;[@Input2]="ERROR";[@Input2]="UNKNOWN");([@[Total Spent]]/[@Quantity]);[@Input2])
```
Ternyata ada **Value error**, dimana sistem tidak bisa melakukan operasi rumus. Setelah saya cek, itu **untuk kolom Total Spent dan Quantity yang kosong**. Baris - baris ini akan menghambat analisis karena setidaknya harus ada kolom Total Spent  atau Quantity atau gabungan keduanya. Oleh karena itu, saya memutuskan untuk menghapus baris yang tidak memiliki kolom Total Spent  dan Quantity. Cara menghapusnya dengan memanfaatkan filter kolom, lalu hapus manual.

#### **Aksi 4**: 
Kolom Price telah sepenuhnya bersih dan diberi nama kolom **Cleaned Price**. Selanjutnya, membereskan kolom Item kembali dengan kode Input 1, hanya mengganti nama kolomnya saja. Kolom ini dinamakan **Input4**. Formulanya:
``` excel
=IFS(AND(OR([@Input1]=0;[@Input1]="ERROR";[@Input1]="UNKNOWN");[@[Cleaned Price]]=1);"Cookie";AND(OR([@Input1]=0;[@Input1]="ERROR";[@Input1]="UNKNOWN");[@[Cleaned Price]]=2);"Coffee";AND(OR([@Input1]=0;[@Input1]="ERROR";[@Input1]="UNKNOWN");[@[Cleaned Price]]=5);"Salad";AND(OR([@Input1]=0;[@Input1]="ERROR";[@Input1]="UNKNOWN");[@[Cleaned Price]]=1.5);"Tea";TRUE;[@Input1])
```
Kenapa mengulang kode ini? ketika proses pertama kode ini dijalankan, tidak semua baris - baris dengan 0, “ERROR” dan “UNKNOWN” terganti, karena ada nilai Price yang kosong dan telah diselesaikan di slide sebelum ini. Oleh karena sekarang kolom Price sudah bersih, maka bisa mengisi baris - baris dengan nilai tersebut.

#### **Aksi 5**: 
Setelah menjalankan kode, nilai 0 tidak semua hilang, karena kode Input 1 hanya mengganti dengan harga tetap yang sama (produk Cookie, Coffee, Salad dan Tea), sedangkan untuk produk - produk dengan harga 3 (Cake & Juice) dan 4 (Smoothie & Sandwich). Untuk mengatasi nilai - nilai ini karena ambigu, saya memutuskan tetap membiarkan apabila nilai Item nya itu “ERROR” atau “UNKNOWN”, dan mengisi menjadi “AMBIGOUS ITEM” untuk nilai kosong. Formulanya
``` excel
=IF([@Input3]=0;"AMBIGOUS ITEM";[@Input3])
```
Jadi untuk Item dengan keterangan “AMBIGOUS ITEM”, “ERROR” atau “UNKNOWN”, jika nilai Price = 3, antara Cake atau Juice, sedangkan bila nilai Price = 4, antara Smoothie atau Sandwich. Sekarang kolom Item sudah bersih, dan diberi nama kolom **Cleaned Item**

---
### **3. Cleaning (Kolom Quantity & Total Spent)**
#### **Aksi 6**: 
Kolom Item sudah bersih, dilanjutkan dengan kolom Quantity. Formulanya:
``` excel
=IF(AND(OR([@Quantity]="";[@Quantity]="ERROR";[@Quantity]="UNKNOWN");NOT(OR([@[Total Spent]]="";[@[Total Spent]]="ERROR";[@[Total Spent]]="UNKNOWN")));[@[Total Spent]]/[@[Cleaned Price]];[@Quantity])
```
Kode di atas berlawanan dengan rumus sebelumnya pada kolom Price, karena di sini yang bermasalah adalah kolom Quantity. Karena kolom Price sudah terisi lengkap, saya membalikkan rumus menjadi: Total Spent/Price. Di kolom ini tidak ada baris dengan nilai 0, ERROR, atau UNKNOWN (karena baris tersebut [bersama nilai Total Spent-nya] telah dihapus sebelumnya) sehingga kolom Quantity kini sudah bersih dan diberi nama kolom **Cleaned Quantity**.

#### **Aksi 7**: 
Karena kolom Price dan Quantity sudah bersih, maka untuk Total Spent, tinggal mengalikan Price dan Quantity. Formulanya:
``` excel
=IF([@[Cleaned Quantity]]=1;([@[Cleaned Price]]*[@[Cleaned Quantity]]);[@[Total Spent]])
```
Sekarang kolom Item, Price, Quantity dan Total Spent sudah bersih (kolom total spent diberi nama **Cleaned Total Spent**.

---
### **4. Cleaning (Kolom Payment & Location)**
#### **Aksi 8**:  
Selanjutnya, membersihkan kolom Payment. Sama seperti kolom - kolom sebelumnya, di kolom Payment ini masih terdapat nilai kosong, “ERROR” dan “UNKNOWN”. Tapi berbeda dengan kolom - kolom sebelumnya, di kolom Payment ini tidak ada pola yang meruju ke nilai aslinya (Digital Wallet, Cash  dan Credit Card). Untuk menghindari bias, saya memutuskan membiarkan nilai “ERROR”, “UNKNOWN”, dan mengisi nilai kosong menjadi “AMBIGOUS PAYMENT.” Formulanya:
``` excel
=IF([@[Payment Method]]=0;"AMBIGOUS PAYMENT";[@[Payment Method]])
```
Kolom diberi nama **Cleaned Payment**.

#### **Aksi 9**:  
Sama seperti kolom Payment, saya memutuskan hal yang sama untuk kolom Location karena memiliki pola yang sama.  Formulanya:
``` excel
=IF([@Location]=0;"AMBIGOUS LOCATION";[@Location])
```
Kolom diberi nama **Cleaned Location**.

---
### **4. Cleaning (Kolom Transaction Date)**
#### **Aksi 10**: 
Untuk nilai kosong, “ERROR” dan “UNKNOWN” pada kolom Transaction Date, saya memutuskan menghapusnya karena akan mengganggu tren harian, mingguan dan bulanan data. Selain itu, data - data anomali itu juga hanya kurang lebih 4% dari total data, jadi masih bisa dilakukan karena proporsinya yang kecil. Cara menghapusnya dengan memanfaatkan filter kolom, lalu hapus manual.

---

### **5. Export Data Bersih**
- **File Output**: `[cleaned_data.xlsx]`  
- **Jumlah Data Akhir**: 9515 baris, 8 kolom.

---

## Impact Cleaning
| **Metric**              | Sebelum | Sesudah |  
|-------------------------|---------|---------|  
| Missing Values          | 10-30%     | 0%      |  

---

## Contact  
Untuk pertanyaan, hubungi: [syifagusriani1@email.com](mailto:syifagusriani1@email.com)  

