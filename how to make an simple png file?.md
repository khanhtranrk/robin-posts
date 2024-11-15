# 16x16.png

## Vấn đề
Vẽ một hình tròn màu vàng trên nền màu đỏ
> Kích thước 16x16
> Làm từ con số 0

## Về PNG

### Về biểu diễn dữ liệu

**Giá trị màu (Color values)**
Các màu (Colors) có thể được biểu diễn bằng thang độ xám (grayscale) hoặc RGB (đỏ, lục, lam) dữ liệu mẫu (sample data). **Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf)**

**Bố cục ảnh (Image layout)**
Một ảnh PNG là một mảng các pixel hình chữ nhật. Kích thước của mỗi pixel được xác định bởi độ sâu của bit (bit depth). Độ sâu của bit (bit depth) là số bit được sử dụng cho mỗi mẫu (sample). **Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf)**

### Về cấu trúc tệp PNG
Một tệp PNG luôn bắt đầu với một chữ kí (signature) và theo sau là các đoạn (chunks).

**Chữ kí của tệp PNG (PNG file signature)**
Tám bytes đầu tiên của tệp PNG luôn chứa các giá trị `137 80 78 71 13 10 26 10` là chữ kí của tệp PNG.

**Bố cục các đoạn (Chunk layout)**
Mỗi đoạn (chunk) luôn chứa bốn phần:

```
Length (4 bytes) | Chunk Type (4 bytes) | Chunk Data | CRC (4 bytes)
```

Length (4 bytes): độ dài của dữ liệu của đoạn (Chunk Data) không bao gồm loại của đoạn (Chunk Type), CRC và chính nó.
Chunk Type (4 bytes): có nhiều loại, tuy nhiên trong bài viết này chủ yếu chỉ nói đến các loại: IHDR, IDAT, IEND.
Chunk Data: dữ liệu của đoạn, nó phụ thuộc vào các loại đoạn khác nhau (Chunk Type).
CRC: được tính theo thuật toán CRC với đầu vào tính loại đoạn (Chunk Type) đến hết dữ liệu đoạn (Chunk Data).

**Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf)**

**CRC**
Các đoạn CRC tính toán bằng cách sử dụng các các phương thức CRC tiêu chuẩn được xác định bởi ISO3309 [ISO-3309] hoặc ITU-T V.42 [ITU-T-V42]. Đa thức CRC `x^32 + x^26 + x^23 + x^22 + x^16 + x^12 + x^11 + x^10 + x^8 + x^7 + x^5 + x^4 + x^2 + x + 1`.

Ví dụ, tính CRC-32 cho đoạn dữ liệu đầu vào là "test".

Đầu tiên cần chuyển dữ liệu đầu vào là "test" về dạng nhị phân, ta được:

```
01110100 01100101 01110011 01110100
```

Sau đó đảo các bit theo từng byte

```
00101110 10100110 11001110 00101110
```

Tiếp theo nối thêm 32bit 0 vào sau đoạn mã

```
00101110 10100110 11001110 00101110 00000000 00000000 00000000 00000000
```

Tiếp đến XOR 4 bytes đầu tiên với 0xFFFFFF

```
11010001 01011001 001100011 10100010 0000000 00000000 00000000 00000000
```

Sau đó, chia đoạn mã nhị phân trên với mã nhị phân có được từ đẳng thức:

```
1101000101011001001100011101000100000000000000000000000000000000
100000100110000010001110110110111
0101001100111001101111110000101010000000000000000000000000000000
 100000100110000010001110110110111
0001001000001001111110000110011101000000000000000000000000000000
   100000100110000010001110110110111
0000001001000101111010011011110000110000000000000000000000000000
      100000100110000010001110110110111
0000000001001100011010111000011101011110000000000000000000000000
         100000100110000010001110110110111
0000000000001101010110111100000000110011110000000000000000000000
            100000100110000010001110110110111
0000000000000101011111011100100011011110011110000000000000000000
             100000100110000010001110110110111
0000000000000001011011101100110010101000101001000000000000000000
               100000100110000010001110110110111
0000000000000000011010100000110110110101000100110000000000000000
                 100000100110000010001110110110111
0000000000000000001010110011110111110010011111101100000000000000
                  100000100110000010001110110110111
0000000000000000000010111010010111010001110010000010000000000000
                    100000100110000010001110110110111
0000000000000000000000111000001111011001001001011001100000000000
                      100000100110000010001110110110111
0000000000000000000000011000101001011011000111101111011000000000
                       100000100110000010001110110110111
0000000000000000000000001000111010011010000000110100000100000000
                        100000100110000010001110110110111
0000000000000000000000000000110011111010100011011001101010000000
                            100000100110000010001110110110111
0000000000000000000000000000010011011100100001010111011100111000
                             100000100110000010001110110110111
0000000000000000000000000000000011001111100000010000000111100100
```

32-bit cuối nhận được chính là mã CRC. Tuy nhiên, cần phải xử lí thêm vài bước.

```
11001111100000010000000111100100
```

Đầu tiên là XOR đoạn mã trên với 0xFFFFFFFF


```
00110000011111101111111000011011
```

Cuối cùng là đảo ngược lại đoạn mã

```
11011000011111110111111000001100
```

Và đó cũng là mã CRC cuối cùng.

## Liên quan

### Adler-32
Adler32 là một thuật toán tổng kiểm tra được sử dụng để phát hiện lỗi trong việc truyền hoặc lưu trữ dữ liệu. Nó tạo ra giá trị băm 32 bit có thể được sử dụng để xác minh tính toàn vẹn dữ liệu.

**Cách tính**

```
An Adler-32 checksum is obtained by calculating two 16-bit checksums A and B and concatenating their bits into a 32-bit integer. A is the sum of all bytes in the stream plus one, and B is the sum of the individual values of A from each step.

At the beginning of an Adler-32 run, A is initialized to 1, B to 0. The sums are done modulo 65521 (the largest prime number smaller than 216). The bytes are stored in network order (big endian), B occupying the two most significant bytes.

The function may be expressed as
    A = 1 + D1 + D2 + ... + Dn (mod 65521)
    B = (1 + D1) + (1 + D1 + D2) + ... + (1 + D1 + D2 + ... + Dn) (mod 65521)
      = n×D1 + (n−1)×D2 + (n−2)×D3 + ... + Dn + n (mod 65521)
    Adler-32(D) = B × 65536 + A
```

**Ví dụ**
Tính Adler-32 cho chuỗi `test`

```
|Character |  ASCII code |        A         |        B        |
|-------------------------------------------------------------|
|    t     |     116     |   1 + 116 = 117  |   0 + 117 = 117 |
|    e     |     101     | 117 + 101 = 218  | 117 + 218 = 335 |
|    s     |     115     | 218 + 115 = 333  | 335 + 333 = 668 |
|    t     |     116     | 333 + 116 = 449  | 668 + 449 = 1117|

A = 449
B = 1117
Output: (B << 16) + A = 0x045d01c1
```

### Huffman
Mã hóa Huffman là một dạng mã hóa tiền tố, được sử dụng để nén dữ liệu không làm mất thông tin.

**Các bước nén dữ liệu với Huffman**
1. Trích xuất các kí tự và tần suất xuất hiện của chúng từ dữ liệu gốc.
2. Xây dựng một cây nhị phân.
3. Trích xuất các mã từ cây.

**Ví dụ**
Dữ liệu gốc `testttee`

Trích xuất kí tự và tần suất xuất hiện

```
t - 4
e - 3
s - 1
```

Xây dựng cây

```
   (8)
   / \
  0   1
 /     \
t      (4)
       / \
      0   1
     /     \
    e       s
```

Trích xuất mã từ cây

```
t - 0
e - 10
s - 11
```

### LZ77
LZ77 là một thuật toán nén không làm mất dữ liệu.

Nó tìm kiếm các byte không nén trùng lặp và thay chúng bằng con trỏ tham chiếu. Con trỏ tham chiếu được xác định bởi hai phần:
- Khoảng cách: số bước dịch về vị trí byte cần sao chép
- Độ dài: số byte cần sao chép

**Ví dụ**
Nén chuỗi `His Bis`

```
          Length: 2
          ---
H|i|s| |B|i|s
  |_______|
  Distance: back 4
          
```

Kết quả sau nén:

```
His B(4,2)
```

### Deflate
Deflate là một thuật toán nén không làm mất dữ liệu. Được sử dụng trong tệp PNG. Nó là sự kết hợp giữa hai thuật toán LZ77 và mã hóa Huffman.

```
Raw data ----> LZ77 Encoding ----> Huffman Encoding ------------>
                                                                |
                                                            Compressed data
                                                                |
Decompressed Data <---- LZ77 Decoding <---- Huffman Decoding <---
```

### Zlib

**Cấu trúc chung**

```
[Header (2 bytes)] | [Copmress data] | [Adler32 (2 bytes)]
```

**Header**

```
[CMF - Compression Method and flags (1 byte)] | [FLG - FLaGs (1 byte)]

CMF: [CM - Compression method (4 bits)] | [CINFO - Compression info (4 bits)]
- CM: phương thức nén. CM = 8 để chỉ phương thức nén 'deflate', được sử dụng trong png và gzip.
- CINFO: thông tin nén. CINFO = 7

FLG: [FCHECK (5 bits)] | [FDICT - 1 bit] | [FLEVEL 2 bits]
- FCHECK: mã kiểm tra CMF và FLG, sao cho CMF * 256 + FLG là bội của 31.
- FDICT: nếu giá trị này được cài đặt, một DICT sẽ nằm sau FLG.
- FLEVEL: Chỉ mức độ nén. CM = 8 có các mức độ nén sau: 0 (fastest algorithm), 1 (fast algorithm), 2 (default algorithm), 3 (slowest algorithm)

Ví dụ:
78 01 - No Compression/low
78 5E - Fast Compression
78 9C - Default Compression
78 DA - Best Compression
```

**Compress data**
Compress data bao gồm nhiều khối (block).

```
|       | |       | |       | |       |
| Block | | Block | | Block | | Block |
|       | |       | |       | |       |
```

Cấu trúc của khối (block)
Mỗi khối sẽ bao gồm một phần header (3 bits):
    - BFINAL (1 bit): nếu bằng 0 có nghĩa là còn block kế tiếp, bằng 1 thì có nghĩa là khối cuối cùng.
    - BTYPE (2 bits):
        00 - no compression
        01 - compressed with fixed Huffman codes
        10 - compressed with dynamic Huffman codes
        11 - reserved (error)

Phần tiếp theo tùy vào loại khác nhau sẽ có định dạng khác nhau.

**Non-compressed (BTYPE = 00)**
Bỏ qua byte chứa header. Bắt đầu ở các bytes tiếp theo sẽ có định dạng như sau:

```
[LEN (2 bytes)] | [(NLEN (2 bytes)] | [data (LEN bytes)]

(NLEN là nghịch đảo của LEN)
```

Ví dụ, nén `test`

Chuyển dữ liệu đầu vào sang nhịphân `01110100 01100101 01110011 01110100`

Nối đoạn dữ liệu với những phần còn lại:

```
HEADER = 00000001 (BFINAL = 1 | BTYPE = 00)
LEN = 00000010 00000000 (4)
NLEN = LEN XOR #FFFF = 11111111 11111101

00000001 00000010 00000000 11111111 11111101 01110100 01100101 01110011 01110100 
|_______||________________||________________||__________________________________|
    |           |                   |                          |
  HEADER       LEN                NLEN                      "test"
```

**Compresed**
Trong khối sẽ có 3 đối tượng được mã hóa: byte dữ liệu thuần túy và cặp (length, backward distance).
Các cặp (length, backward distance) là kết quả của nén LZ77.

```
0 - 255: dùng để biểu diễn byte dữ liệu thuần túy
256: dùng để mã đánh dấu kết thúc của block.
257 - 285: dùng để biểu diễn độ dài (Xem hai bảng ánh xạ bên dưới)
```

Dưới đây là các bảng quy ước ánh xạ:

** Bảng ánh xạ biểu diễn độ dài
(Code được biểu diễn tùy thuộc vào loại compress được sử dụng)

```
                 Extra          |       Extra         |        Extra
            Code Bits Length(s) |  Code Bits Lengths  |   Code Bits Length(s)
            ---- ---- ------    |   ---- ---- ------- |    ---- ---- -------
             257   0     3      |   267   1   15,16   |    277   4   67-82
             258   0     4      |   268   1   17,18   |    278   4   83-98
             259   0     5      |   269   2   19-22   |    279   4   99-114
             260   0     6      |   270   2   23-26   |    280   4  115-130
             261   0     7      |   271   2   27-30   |    281   5  131-162
             262   0     8      |   272   2   31-34   |    282   5  163-194
             263   0     9      |   273   3   35-42   |    283   5  195-226
             264   0    10      |   274   3   43-50   |    284   5  227-257
             265   1  11,12     |   275   3   51-58   |    285   0    258
             266   1  13,14     |   276   3   59-66
```

** Bảng ánh xạ biểu diễn khoảng cách
(Code được biểu diễn bằng 5 bits)

```
                  Extra     |        Extra         |        Extra
             Code Bits Dist |   Code Bits   Dist   |    Code Bits Distance
             ---- ---- ---- |   ---- ----  ------  |    ---- ---- --------
               0   0    1   |    10   4     33-48  |    20    9   1025-1536
               1   0    2   |    11   4     49-64  |    21    9   1537-2048
               2   0    3   |    12   5     65-96  |    22   10   2049-3072
               3   0    4   |    13   5     97-128 |    23   10   3073-4096
               4   1   5,6  |    14   6    129-192 |    24   11   4097-6144
               5   1   7,8  |    15   6    193-256 |    25   11   6145-8192
               6   2   9-12 |    16   7    257-384 |    26   12  8193-12288
               7   2  13-16 |    17   7    385-512 |    27   12 12289-16384
               8   3  17-24 |    18   8    513-768 |    28   13 16385-24576
               9   3  25-32 |    19   8   769-1024 |    29   13 24577-32768
```

Khối Compressed có 2 cách biểu diễn. Một là biểu diễn theo mã Huffman tĩnh, hai là biễu diễn theo mã Huffman động.

**Compress with fixed Huffman codes (BTYPE = 01)**
Với cách biểu diễn này, sẽ có bảng mã quy ước ánh xạ như sau:

```
                   Lit Value    Bits        Codes
                   ---------    ----        -----
                     0 - 143     8          00110000 through
                                            10111111
                   144 - 255     9          110010000 through
                                            111111111
                   256 - 279     7          0000000 through
                                            0010111
                   280 - 287     8          11000000 through
                                            11000111
```

Ví dụ, nén chuỗi `test`

Mã accii của từng kí tự trong chuỗi test:

```
116 101 115 116
```

Ánh xạ mã trên dựa trên bảng ánh xạ phía trên được:

```
10100100 10010101 10100011 10100100
```

Nối các bit trên thành một dải các byte

```
00101011 01001001 00101101 00101011 00000001 00000000
|___||_| |__________________________________________|
 CPD Header           Compressed data
```

**Compress with dynamic Huffman codes (BTYPE == 10)**
Cấu trúc:

```
[HLIT (5 bits)] [HDIST (5 bits)] [CLEN (4 bits)] [Code Length Alphabet Table (CLEN * 3 bits)] [Code Length Table] [Compressed Data]
```

Với cách này cần xây dựng cây Huffman.

Ví dụ, nén chuỗi `testtest`

Nén theo thuật toán LZ77

```
test(5, 4)
```

Xây dựng cây Huffman cho các kí tự thuần túy và các độ dài (length):
(Độ dài (length) được nhắc tới ở đây là một trong hai thành phần của con trỏ từ thuật toán LZ77)

Ở đâu chúng ta có 5 kí tự xuất hiện trong mã của mình lần lượt với thứ tự và tần số như sau:

```
    e    s    t   (256)  (258) 
    1    1    2     1      1 

    256: là mã kết thúc block
    258: là ánh xạ của length 4 (có thể xem ở bảng ánh xạ ở phần trên)
```
Sắp xếp chúng lại theo tăng dần của tần số:

```
    t    e    s   (256)  (258)
    2    1    1     1      1   
```

Gộp các kí tự theo từng bước:

```
    t    e    s   (256)  (258)
    2    1    1     1      1  

#1 Gộp (256) và (258) thành a
    t  (a)   e    s   
    2   2    1    1   

#2 Gộp e và s thành (b)
    t  (a)  (b) 
    2   2    2 

#3 Gộp (a) và (b) thành (c)
   (c)  t      
    5   2 

#4 Gộp t và (b) thành (d)
   (d)
    7
```

Tiếp theo, xây dựng bảng chi tiết các bước để tìm độ dài mã cho từng kí tự

```
    #0   #1   #2   #3   clen
e   0    0    1b   2c     3d
s   0    0    1b   2c     3d
t   0    0    0    0      1d
256 0    1a   1a   2c     3d
258 0    1a   1a   2c     3d
```

Đặc tả của DEFLATE cho biết cần đọc tần số xuất hiện của các độ dài:

```
N   bl_count[N]
0       0
1       1
2       0
3       4 
```

Tiếp theo cần xây các mã đầu tiên để dựng được bảng mã huffman:

```
N   bl_count[N] next_code[N]
0       0       
1       1       0 0
2       0       2 10
3       2       6 100
```

Đối với mỗi lần sử dụng chỉ cần tăng mã lên 1 sẽ được bảng mã như sau:

```
    freq clen code
e     1    3  100
s     1    3  101
t     2    1  0
256   1    3  110
258   1    3  111
```

Xây dựng cây huffman cho con trỏ của thuật toán LZ77. Nhưng do chỉ có 1 con trỏ nên cho ra kết quả như sau:

```
5 1 - 0
```

Xây dựng bảng độ dài mã của cho từng kí tự 'kí tự'.

```
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 3 0 0 0 0 0 0 0 0 0 0 0 0 0 3 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 3 

0 0 0 0 1
```

Biểu diễn các kí trên sang bảng chữ cái dành độ dài mã (Code length alphabet)

```
The alphabet for code lengths is as follows:
    0 - 15: Represent code lengths of 0 - 15
        16: Copy the previous code length 3 - 6 times.
            The next 2 bits indicate repeat length
                  (0 = 3, ... , 3 = 6)
               Example:  Codes 8, 16 (+2 bits 11),
                         16 (+2 bits 10) will expand to
                         12 code lengths of 8 (1 + 6 + 5)
        17: Repeat a code length of 0 for 3 - 10 times.
            (3 bits of length)
        18: Repeat a code length of 0 for 11 - 138 times
            (7 bits of length)
```

```
HLIT = 259
HDIST = 5

18/101 3 18/13 3 1 18/138 0 3 17/4 3
```

Xây dựng cây Huffman cho các mã trên:

```
Các kí tự và tần số:
0 1 3 17 18
1 1 4 1  3

Sắp xếp theo tần số:
3 18 0 1 17
4 3  1 1 1 

    #0   #1   #2   #3   #3
0   0    0    1b   2c   3d
1   0    1a   2b   3c   4d
3   0    0    0    0    1d
18  0    0    0    1c   2d
17  0    1a   2b   3c   4d


N   bl_count[N]
0       0
1       1
2       1
3       1 
4       2

N   bl_count[N] next_code[N]
0       0       
1       1       0  0
2       1       2  10
3       1       6  110
3       2       14 1110

    freq clen code
0     1    3  110
1     1    4  1110
3     4    1  0
17    1    4  1111
18    3    2  10
```
Sắp xếp các mã trên theo thứ tự:

```
16 17 18 0 8 7 9 6 10 5 11 4 12 3 13 2 14 1 15
0  4  2  3 0 0 0 0 0  0 0  0 0  1 0  0 0  4 0
```

Giờ sẽ xây dựng nội dung nén cho phần này.

```
HLIT = 259
HDIST = 5
HCLEN = 18

header: 110
hlit = 00010
hdist = 00011
hclen = 1110
hclentbl: 000 100 010 011 000 000 000 000 000 000 000 000 000 001 000 000 000 100

# 18/101 3 18/13 3 1 18/138 0 3 17/4 3
clenaltbl:  10 1011010 0 10 0001101 0 110 10 1111111 110 0 1111 000 0

    freq clen code
e     1    3  100
s     1    3  101
t     2    1  0
256   1    3  110
258   1    3  111

# test(5, 4)
cmpdt: 0 100 101 0 110 111 0

end-block: 110
```
(Ví dụ chỉ mang tính chất minh họa, trong thực tế phương pháp này chỉ áp dụng cho tập dữ liệu từ 32kb trở lên))

## Tạo 16x16.png
### Đoạn IHDR (IHDR chunk)
Đoạn IHDR dùng để chứa các thông tin cơ bản của hình ảnh. Chi tiết ở sẽ được nêu ở phía dưới.

Dữ liệu của đoạn IHDR chứa các trường dữ liệu:
    - Chiều ngang (Width): 4 bytes
    - Chiều cao (Height): 4 bytes
    - Độ sâu của bit (Bit depth): 1 bytes
    - Loại màu (Color type): 1 bytes
    - Phương thức nén (Compression method): 1 bytes
    - Phương thức lọc (Filter method): 1 bytes
    - Phương thức đan xen (Interlace method): 1 bytes

Như vấn đề đã nêu, kích thước ảnh là 16x16 nên trường dữ liệu chiều ngang và chiều cao sẽ là 16.

Độ sâu của bit (Bit depth) là một số nguyên chỉ số bit được sử dụng cho một mẫu (sample). Các giá trị có thể chọn cho trường dữ liệu này là 1, 2, 3, 4, 8, 16. Để đơn giản chúng ta chọn giá trị này là 8.

Lọai màu (Color type) là một số nguyên dùng để mô tả dữ liệu hình ảnh. Các giá trị có thể chọn cho trường dữ liệu này bao gồm: 0, 1, 2, 3, 4 và 6 **(Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf))**
. Ở đây chúng ta chọn giá trị này là 2. Để hình ảnh có thể hiện được các màu sắc.

Phương thức nén (Compression method) là một số nguyên để chỉ phương thức nén sẽ được sử dụng để nén dữ liệu ảnh. Hiện tại, chỉ có thể chọn giá trị 0 cho trường dữ liệu này.

Phương thức lọc (Filter method) là một số nguyên để chỉ phương thức tiền xử lí được áp dụng cho hình ảnh trước khi được nén. Hiện tại, chỉ có thể chọn giá trị 0 cho trường dữ liệu này.

Phương thức đan xen (Interlace method) là một số nguyên cho biết thứ tự truyền dữ liệu hình. Có hai giá trị có thể chọn là 0 và 1 **(Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf))**. Để đơn giản chúng ta sẽ chọn giá trị là 0.

Đây là thông tin của đoạn IHDR của chúng ta:
    - Width: 16
    - Height: 16
    - Bit depth: 8
    - Color type: 2
    - Compression method: 0
    - Filter method: 0
    - Interlace method: 0

Giờ, từ các thông tin trên chúng ta sẽ xây dựng một chuỗi mã cho phần dữ liệu đoạn (Data chunk) cho đoạn IDHR này. Ở đây để thuận tiện cho việc trình bày chúng ta sẽ thể hiện dữ liệu bằng mã hexa. Theo đúng thứ tự từ trên xuống dưới chúng ta nối các dữ liệu với được chuỗi dữ liệu như sau:

```
0000 0010 0000 0010 0802 0000 00
```

Tiếp đến chúng ta sẽ nối thêm phần loại đoạn (Chunk type) vào phần dữ liễu trên.

```
4948 4452 0000 0010 0000 0010 0802 0000 00
```

Tiếp theo là phần độ dữ liệu. Dễ thấy dữ liệu có độ dài là 13. Nên chúng ta được dữ liệu:

```
0000 000D 4948 4452 0000 0010 0000 0010 0802 0000 00
```

Cuối cùng là tính CRC. CRC được tính từ phần dữ liệu của Chunk type đến kết thúc Chunk data. Sau khi tính toán chúng ta nhận được kết quả là `9091 6836`. Nối kết quả vào các phần dữ liệu khác được:

```
0000 000D 4948 4452 0000 0010 0000 0010 0802 0000 0090 9168 36
```

Đây cũng là đoạn dữ liệu IHDR hoàn chỉnh của chúng ta.

Tiếp đến sẽ xây dựng các đoạn cần thiết còn lại là IDAT và IEND.

### Đoạn IDAT (IDAT chunk)

Đây là đoạn chứa các dữ liệu hình ảnh thực tế. Để tạo được dữ liệu cho đoạn này cần:
1. Xây dựng dữ liệu thô như phần bố cục ảnh (image layout) đã nói ở phía trên theo các thông số chiều rộng, chiều dài ở đoạn IHDR.
2. Tiền sử xử lí dữ liệu theo phương thức lọc đã chọn ở đoạn IHDR.
3. Nén dữ liệu hình ảnh theo các phương thức nén đã chọn ở đoạn IHDR.

Đầu tiên, chúng ta sẽ xây dựng dữ liệu thô theo yêu cầu của vấn đề. Chúng ta sẽ xây dựng một mảng hình vuông với số dòng tương ứng với chiều cao, ở đây là 16 và số cột tương ứng với chiều rộng, ở đây cũng là 16.

Dễ thấy sẽ chỉ hai màu được sử dụng là màu đỏ và màu vàng. Do độ sâu của bit đã chọn là 8 và loại màu là 2. Nên chúng ta sẽ biểu diễn mỗi pixel của theo RGB với ba mẫu (đỏ, lục lam), mỗi mẫu có 8 bit. Vậy, chúng ta có thể nhận được mã màu tương ứng với hai màu đỏ và vàng là "FF0000" và "FFFF00" (do màu vàng là kết hợp tối đa màu đỏ và màu xanh).

Việc tiếp theo, cần xác định mỗi pixel nên được tô màu vàng hay màu đỏ. Để dễ dàng, chúng ta chỉ cần xác định rằng, nếu pixel đang xét thuộc hình tròn thì tô màu vàng còn ngược lại thì tô màu đỏ. Chúng ta có thể xác định một pixel có thuộc hình tròn hay không bằng cách tính khoảng cách của pixel đến tâm hình tròn theo công thức Pytharo.

```
d = sqrt((xp - xc)^2 + (yp - yc)^2)
```

Nếu khoảng cách tính được lớn hơn bán kính hình tròn thì pixel không thuộc hình tròn và ngược lại (Chúng ta có thể giản lược phép tính bằng cách so sánh độ dài trước khi bị lấy căn bậc 2 với bình phương bán kính để tiện cho việc tính toán).

Sau khi, tính toán chúng ta có thể nhận được một mảng hình vuông các pixel như sau:

```
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
```

Sau khi có được dữ liệu thô, chúng ta sẽ tiền xử lí dữ liệu bằng cách chỉ định cách lọc cho mỗi dòng dữ liệu. Có nhiều chỉ định, tuy nhiên để đơn giản chúng ta sẽ chọn chỉ định là loại 0, với loại này dữ liệu sẽ không bị thay đổi. **Tìm hiểu chi tiết [tại đây](http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf)**

Chúng ta chỉ cần chèn chỉ định vào đầu mỗi dòng dữ liệu. Sau tiền xử lí, dữ liệu của chúng ta sẽ như sau:

```
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FFFF00 FFFF00 FFFF00 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FFFF00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
00 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000 FF0000
```

Cuối cùng chúng ta sẽ nén dữ liệu theo phương thức nén đã chọn ở đoạn IHDR (hiện tại chỉ có một lựa chọn duy nhất). PNG nén dữ liệu theo thuật toán Deflate của zlip **(Tìm hiểu chi tiết [tại đây](https://www.zlib.net/feldspar.html))**. Thế nên chúng ta sẽ nén dữ liệu hình ảnh trên bằng zlib. Các bước nén như sau:

Do để đơn giản chúng ta sẽ nén theo cây Huffman tĩnh (Fixed Huffman). Nhưng trước đó hãy thực hiện nén dữ liệu theo ý tưởng của thuật toán LZ77. Nhìn vào đoạn mã có rất nhiều đoạn trùng nhau.


```
00 FF0000 <3, 45>
00 FF0000 <3, 45>
00 FF0000 <3, 45>
00 FF0000 <3, 45>
00 FF0000 <3, 21> FFFF00 FF0000 <3, 18>
00 FF0000 <3, 15> FFFF00 <3, 12> FF0000 <3, 12>
00 FF0000 <3, 12> FFFF00 <3, 18> FF0000 <3, 9>
00 FF0000 <3, 12> FFFF00 <3, 18> FF0000 <3, 9>
00 FF0000 <3, 9> FFFF00 <3, 24> FF0000 <3, 6>
00 FF0000 <3, 12> FFFF00 <3, 18> FF0000 <3, 9>
00 FF0000 <3, 12> FFFF00 <3, 18> FF0000 <3, 9>
00 FF0000 <3, 15> FFFF00 <3, 12> FF0000 <3, 12>
00 FF0000 <3, 21> FFFF00 FF0000 <3, 18>
00 FF0000 <3, 45>
00 FF0000 <3, 45>
00 FF0000 <3, 45>
```

Tiếp theo là mã hóa bằng cây Huffman tĩnh.
Chúng ta sẽ ánh xạ các mã trên dựa các bảng ánh xạ có nêu ở phần Huffman.
Và chỉ mã hóa toàn bộ dữ liệu trong một block.

```
                 Extra          |       Extra         |        Extra
            Code Bits Length(s) |  Code Bits Lengths  |   Code Bits Length(s)
            ---- ---- ------    |   ---- ---- ------- |    ---- ---- -------
             257   0     3      |   267   1   15,16   |    277   4   67-82
             258   0     4      |   268   1   17,18   |    278   4   83-98
             259   0     5      |   269   2   19-22   |    279   4   99-114
             260   0     6      |   270   2   23-26   |    280   4  115-130
             261   0     7      |   271   2   27-30   |    281   5  131-162
             262   0     8      |   272   2   31-34   |    282   5  163-194
             263   0     9      |   273   3   35-42   |    283   5  195-226
             264   0    10      |   274   3   43-50   |    284   5  227-257
             265   1  11,12     |   275   3   51-58   |    285   0    258
             266   1  13,14     |   276   3   59-66
```

```
                  Extra     |        Extra         |        Extra
             Code Bits Dist |   Code Bits   Dist   |    Code Bits Distance
             ---- ---- ---- |   ---- ----  ------  |    ---- ---- --------
               0   0    1   |    10   4     33-48  |    20    9   1025-1536
               1   0    2   |    11   4     49-64  |    21    9   1537-2048
               2   0    3   |    12   5     65-96  |    22   10   2049-3072
               3   0    4   |    13   5     97-128 |    23   10   3073-4096
               4   1   5,6  |    14   6    129-192 |    24   11   4097-6144
               5   1   7,8  |    15   6    193-256 |    25   11   6145-8192
               6   2   9-12 |    16   7    257-384 |    26   12  8193-12288
               7   2  13-16 |    17   7    385-512 |    27   12 12289-16384
               8   3  17-24 |    18   8    513-768 |    28   13 16385-24576
               9   3  25-32 |    19   8   769-1024 |    29   13 24577-32768
```

```
                   Lit Value    Bits        Codes
                   ---------    ----        -----
                     0 - 143     8          00110000 through
                                            10111111
                   144 - 255     9          110010000 through
                                            111111111
                   256 - 279     7          0000000 through
                                            0010111
                   280 - 287     8          11000000 through
                                            11000111
```

Với đoạn 00 FF0000 <3, 45> sẽ được mã hóa như sau:
Dựa vào những bản trên ta ánh xạ như sau:

```
00 -> 00110000
FF -> 111111111
len = 45 -> 274 + (3 bits = 010) -> 0010010 010
dist = 3 (cố định 5 bits) -> 00011
```

Kết quả:

```
00110000 111111111 00110000 00110000 0010010 010 00011
```

(Các đoạn còn lại cũng được thực hiện tương tự)

Tuy nhiên cách sắp xếp các bit trong thực tế không theo thứ tự như vậy. Giờ hãy nối head vào block và sắp xếp lại các bit.

```
Block Header:
BFINAL = 1 (Vì đây là block duy nhất)
BTYPE = 01 (Vì chúng ta sử dụng mã hóa theo Huffman tĩnh)

Các đoạn mã sẽ được nối lại như sau:
01100011 11111000 11001111 11000000 01000000 00010010 110 ...

```

(Các đoạn mã còn lại cũng được nối theo cách tương tự)

Kết quả chúng ta sẽ nhân được block như sau:

```
01100011 11111000 11001111 11000000 01000000 00010010 01100010 11111000 11001111 11000000 01000000 00010010 01100010 11111000 11001111 11000000
01000000 00010010 01100010 11111000 11001111 11000000 01000000 00010010 01100010 11111000 11001111 11000000 10000000 00010101 11111101 11111111
11001111 11110000 10011111 10000001 00000001 00010011 00110001 11111100 01100111 01100000 01000000 01000011 11111111 11111111 00110011 00100000
10100011 11111111 00001100 00001100 11001000 10001000 11100001 00111111 00000011 00000011 00110010 11111010 11111111 10011111 00000001 00010011
11111101 01100111 01100000 10000000 00100011 10000110 11111111 00001100 00001100 11001000 11101000 11111111 01111111 00000110 01001100 11110100
10011111 10000001 00000001 10001110 00011000 11111110 00110011 00110000 11000000 11010001 11111111 11111111 00001100 10111000 11010000 01111111
00000110 00000110 00001000 01100010 11111000 11001111 11000000 10000000 10001100 11111110 11111111 01100111 11000000 01000100 11111111 00011001
00011000 11100000 10001000 11100001 00111111 00000011 00000011 00110010 11111010 11111111 10011111 00000001 00010011 11111101 01100111 01100000
10000000 00100011 10000110 11111111 00001100 00001100 01101000 11101000 11111111 01111111 00000110 01100100 11110100 10011111 10000001 00000001
00011001 00110001 11111100 01100111 01100000 11000000 10001010 11111110 11111111 01100111 11111000 11001111 11000000 10000000 10001001 00011000
11111110 00110011 00110000 10010000 10000100 00011000 11111110 00110011 00110000 10010000 10000100 00011000 11111110 00110011 00110000 10010000
10000100 00000000 

```

Giờ thì hãy hoàn thành phần dữ nén bằng cách nối thêm header và mã kiểm tra adler32
Tính mã Adler cho dữ liệu gốc ban đầu:

```
|    Byte    |                    A                     |                    B                     |
|          0 |                                1 + 0 = 1 |                                0 + 1 = 1 |
|        255 |                            1 + 255 = 256 |                                1 + 1 = 2 |
|          0 |                            256 + 0 = 256 |                          257 + 256 = 513 |
|          0 |                            256 + 0 = 256 |                          513 + 256 = 769 |
...          ...                                        ...                                        ...
|          0 |                        77521 + 0 = 77521 |              29918145 + 77521 = 29995666 |
|        255 |                      77521 + 255 = 77776 |              29995666 + 77521 = 30073187 |
|          0 |                        77776 + 0 = 77776 |              30073442 + 77776 = 30151218 |
|          0 |                        77776 + 0 = 77776 |              30151218 + 77776 = 30228994 |

A = A mod 65521 = 77776 mod 65521 = 5704
B = B mod 65521 = 30228994 mod 65521 = 4618
Adler32 = B * 65536 + A = 23813*65536 + 12255 = 1560621023
Result: 5d052fdf
```

Các phần trong như sau

```
HEADER: 789c (01111000 10011100)
Adler32: 5d05 2fdf
Data:
    63f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 8015 fdff cff0 9f81 0113 31fc 6760 4043 ffff 3320 a3ff 0c0c c888 e13f
    0303 32fa ff9f 0113 fd67 6080 2386 ff0c 0cc8 e8ff 7f06 4cf4 9f81 018e 18fe 3330 c0d1 ffff 0cb8 d07f 0606 0862 f8cf c080 8cfe ff67 c044 ff19
    18e0 88e1 3f03 0332 faff 9f01 13fd 6760 8023 86ff 0c0c 68e8 ff7f 0664 f49f 8101 1931 fc67 60c0 8afe ff67 f8cf c080 8918 fe33 3090 8418 fe33
    3090 8418 fe33 3090 8400
```

Kết quả sau khi nối

```
    789c 63f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 8015 fdff cff0 9f81 0113 31fc 6760 4043 ffff 3320 a3ff 0c0c c888
    e13f 0303 32fa ff9f 0113 fd67 6080 2386 ff0c 0cc8 e8ff 7f06 4cf4 9f81 018e 18fe 3330 c0d1 ffff 0cb8 d07f 0606 0862 f8cf c080 8cfe ff67 c044
    ff19 18e0 88e1 3f03 0332 faff 9f01 13fd 6760 8023 86ff 0c0c 68e8 ff7f 0664 f49f 8101 1931 fc67 60c0 8afe ff67 f8cf c080 8918 fe33 3090 8418
    fe33 3090 8418 fe33 3090 8400 5d05 2fdf
```

Thế là chúng ta đã có được dữ liệu cho đoạn này. Tiếp theo chúng ta sẽ xây dựng đoạn này bằng cách nối dữ liệu trên với các phần còn lại (tương tự như đoạn IHDR)

Đầu tiên là nối loại đoạn. Loại đoạn của đoạn này có mã là `4944 4154`. Đoạn mã trở thành:

```
    4944 4154 789c 63f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 8015 fdff cff0 9f81 0113 31fc 6760 4043 ffff 3320 a3ff
    0c0c c888 e13f 0303 32fa ff9f 0113 fd67 6080 2386 ff0c 0cc8 e8ff 7f06 4cf4 9f81 018e 18fe 3330 c0d1 ffff 0cb8 d07f 0606 0862 f8cf c080 8cfe
    ff67 c044 ff19 18e0 88e1 3f03 0332 faff 9f01 13fd 6760 8023 86ff 0c0c 68e8 ff7f 0664 f49f 8101 1931 fc67 60c0 8afe ff67 f8cf c080 8918 fe33
    3090 8418 fe33 3090 8418 fe33 3090 8400 5d05 2fdf
```

Tiếp đến là phần kích thước dữ liệu. Độ dài dữ liệu ở đây là 32. Nên đoạn mã trở thành:

```
    0000 00b8 4944 4154 789c 63f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 8015 fdff cff0 9f81 0113 31fc 6760 4043 ffff
    3320 a3ff 0c0c c888 e13f 0303 32fa ff9f 0113 fd67 6080 2386 ff0c 0cc8 e8ff 7f06 4cf4 9f81 018e 18fe 3330 c0d1 ffff 0cb8 d07f 0606 0862 f8cf
    c080 8cfe ff67 c044 ff19 18e0 88e1 3f03 0332 faff 9f01 13fd 6760 8023 86ff 0c0c 68e8 ff7f 0664 f49f 8101 1931 fc67 60c0 8afe ff67 f8cf c080
    8918 fe33 3090 8418 fe33 3090 8418 fe33 3090 8400 5d05 2fdf
```

CRC của đoạn mã sau khi tính là `77cc 075c`.

Cuối cùng, đoạn IDAT hoàn chỉnh sẽ như sau:

```
    0000 00b8 4944 4154 789c 63f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 4012 62f8 cfc0 8015 fdff cff0 9f81 0113 31fc 6760 4043 ffff
    3320 a3ff 0c0c c888 e13f 0303 32fa ff9f 0113 fd67 6080 2386 ff0c 0cc8 e8ff 7f06 4cf4 9f81 018e 18fe 3330 c0d1 ffff 0cb8 d07f 0606 0862 f8cf
    c080 8cfe ff67 c044 ff19 18e0 88e1 3f03 0332 faff 9f01 13fd 6760 8023 86ff 0c0c 68e8 ff7f 0664 f49f 8101 1931 fc67 60c0 8afe ff67 f8cf c080
    8918 fe33 3090 8418 fe33 3090 8418 fe33 3090 8400 5d05 2fdf 77cc 075c
```

### Đoạn IEND
Đoạn IEND (IEND chunk) dùng để báo kết thúc một tệp PNG. Đoạn này không có dữ liệu, nên chỉ cần xây dựng bằng cách nối ba phần còn lại.

Độ dài dữ liệu sẽ là 0. Loại đoạn có mã là `4945 4E44`. CRC sao khi tính toán sẽ là `AE42 6082`.

Đoạn IEND hoàn chỉnh sẽ như sau:

```
0000 0000 4945 4E44 AE42 6082
```

### Hoàn chỉnh tệp 16x16.png

Giờ, bằng cách nối các đoạn trên lại cùng với chữ kí của tệp PNG chúng ta sẽ được một tệp PNG hoàn chỉnh.

Các phần sẽ được nối theo biểu diễn phía dưới:

```
[PNG file signiture] [IHDR chunk] [IDAT chunk] [IEND chunk]
```

Cuối cùng tệp PNG hoàn chỉnh của chúng ta sẽ có dữ liệu như sau:

```
    8950 4E47 0D0A 1A0A 0000 000D 4948 4452 0000 0010 0000 0010 0802 0000 0090 9168 3600 0000 b849 4441 5478 9c63 f8cf c040 1262 f8cf c040 1262
    f8cf c040 1262 f8cf c040 1262 f8cf c080 15fd ffcf f09f 8101 1331 fc67 6040 43ff ff33 20a3 ff0c 0cc8 88e1 3f03 0332 faff 9f01 13fd 6760 8023
    86ff 0c0c c8e8 ff7f 064c f49f 8101 8e18 fe33 30c0 d1ff ff0c b8d0 7f06 0608 62f8 cfc0 808c feff 67c0 44ff 1918 e088 e13f 0303 32fa ff9f 0113
    fd67 6080 2386 ff0c 0c68 e8ff 7f06 64f4 9f81 0119 31fc 6760 c08a feff 67f8 cfc0 8089 18fe 3330 9084 18fe 3330 9084 18fe 3330 9084 005d 052f
    df77 cc07 5c00 0000 0049 454E 44AE 4260 82
```

## Tham khảo
1. http://www.libpng.org/pub/png/spec/1.2/png-1.2.pdf
2. https://en.wikipedia.org/wiki/PNG
3. https://www.zlib.net/feldspar.html
4. https://en.wikipedia.org/wiki/Cyclic_redundancy_check
5. https://en.wikipedia.org/wiki/Adler-32
6. https://jnior.com/deflate-compression-algorithm
7. https://calmarius.net/index.php?lang=en&page=programming%2Fzlib_deflate_quick_reference
8. https://www.rfc-editor.org/rfc/rfc1950
9. https://www.rfc-editor.org/rfc/rfc1951
