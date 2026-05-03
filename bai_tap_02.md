Họ Tên : Nguyễn Hữu Văn Tuân

MSSV : K235480106075

Lớp : K59KMT.K01

- Sau đây em xin phép được trình bày Bài tập 02

  # Phần 1: Thiết kế và Khởi tạo Cấu trúc Dữ liệu (Kiến thức 6, 7)
```
Phần Code
-1.1. Tạo Database
CREATE DATABASE [QuanLyBanHang_K235480106075];
GO
USE [QuanLyBanHang_K235480106075];
GO
-- Bảng KhachHang
CREATE TABLE [KhachHang]
(
    [MaKhachHang] INT IDENTITY(1,1) NOT NULL,
    [TenKhachHang] NVARCHAR(100) NOT NULL,
    [SoDienThoai] VARCHAR(15) NULL,
    [DiaChi] NVARCHAR(200) NULL,
    CONSTRAINT [PK_KhachHang] PRIMARY KEY ([MaKhachHang])
);
GO
-- Bảng nhân viên
CREATE TABLE [NhanVien]
(
    [MaNhanVien] INT IDENTITY(1,1) NOT NULL,
    [TenNhanVien] NVARCHAR(100) NOT NULL,
    [ChucVu] NVARCHAR(50) NULL,
    CONSTRAINT [PK_NhanVien] PRIMARY KEY ([MaNhanVien])
);
GO
-- Bảng sản phẩn
CREATE TABLE [SanPham]
(
    [MaSanPham] INT IDENTITY(1,1) NOT NULL,
    [TenSanPham] NVARCHAR(100) NOT NULL,
    [DonGia] DECIMAL(18,2) NOT NULL,
    [SoLuongTon] INT NOT NULL,
    [TrangThai] BIT NOT NULL DEFAULT 1,
    CONSTRAINT [PK_SanPham] PRIMARY KEY ([MaSanPham]),
    CONSTRAINT [CK_SanPham_DonGia] CHECK ([DonGia] > 0),
    CONSTRAINT [CK_SanPham_SoLuongTon] CHECK ([SoLuongTon] >= 0)
);
GO
-- Bảng hóa đơn
CREATE TABLE [HoaDon]
(
    [MaHoaDon] INT IDENTITY(1,1) NOT NULL,
    [MaKhachHang] INT NOT NULL,
    [MaNhanVien] INT NOT NULL,
    [NgayLap] DATE NOT NULL DEFAULT GETDATE(),
    [TongTien] DECIMAL(18,2) NOT NULL DEFAULT 0,
    CONSTRAINT [PK_HoaDon] PRIMARY KEY ([MaHoaDon]),
    CONSTRAINT [FK_HoaDon_KhachHang] FOREIGN KEY ([MaKhachHang]) REFERENCES [KhachHang]([MaKhachHang]),
    CONSTRAINT [FK_HoaDon_NhanVien] FOREIGN KEY ([MaNhanVien]) REFERENCES [NhanVien]([MaNhanVien])
);
GO
-- Bảng chi tiết hóa đơn
CREATE TABLE [ChiTietHoaDon]
(
    [MaChiTiet] INT IDENTITY(1,1) NOT NULL,
    [MaHoaDon] INT NOT NULL,
    [MaSanPham] INT NOT NULL,
    [SoLuongBan] INT NOT NULL,
    [ThanhTien] DECIMAL(18,2) NOT NULL DEFAULT 0,
    CONSTRAINT [PK_ChiTietHoaDon] PRIMARY KEY ([MaChiTiet]),
    CONSTRAINT [FK_CTHD_HoaDon] FOREIGN KEY ([MaHoaDon]) REFERENCES [HoaDon]([MaHoaDon]),
    CONSTRAINT [FK_CTHD_SanPham] FOREIGN KEY ([MaSanPham]) REFERENCES [SanPham]([MaSanPham]),
    CONSTRAINT [CK_CTHD_SoLuongBan] CHECK ([SoLuongBan] > 0)
);
GO
-- Dữ liệu mẫu ban đầu
INSERT INTO [KhachHang]([TenKhachHang],[SoDienThoai],[DiaChi]) VALUES
(N'Nguyen Van An','0901111111',N'Thai Nguyen'),
(N'Tran Thi Bich','0902222222',N'Ha Noi'),
(N'Le Van Cuong','0903333333',N'Bac Ninh'),
(N'Pham Thi Dung','0904444444',N'Hai Phong'),
(N'Hoang Minh Duc','0905555555',N'Nam Dinh');
GO

INSERT INTO [NhanVien]([TenNhanVien],[ChucVu]) VALUES
(N'Nguyen Hai Nam',N'Thu Ngan'),
(N'Tran Thu Ha',N'Quan Ly'),
(N'Le Minh Quan',N'Nhan Vien Ban Hang');
GO

INSERT INTO [SanPham]([TenSanPham],[DonGia],[SoLuongTon],[TrangThai]) VALUES
(N'Ban Phim Co',450000,20,1),
(N'Chuot Khong Day',250000,25,1),
(N'Tai Nghe Bluetooth',650000,15,1),
(N'USB 64GB',180000,30,1),
(N'Man Hinh 24 Inch',2800000,10,1);
GO

INSERT INTO [HoaDon]([MaKhachHang],[MaNhanVien],[NgayLap]) VALUES
(1,1,'2025-05-01'),
(2,2,'2025-05-02'),
(3,1,'2025-05-03');
GO
```
<img width="553" height="311" alt="image" src="https://github.com/user-attachments/assets/053173e5-980e-4422-a439-0fd839c811e7" />
Giải thích khóa chính, khóa ngoại và ràng buộc kiểm tra

Trong hệ thống quản lý bán hàng, để đảm bảo tính toàn vẹn dữ liệu, em sử dụng ba loại ràng buộc chính là Primary Key, Foreign Key và Check Constraint.

* Khóa chính (Primary Key - PK)

Khóa chính dùng để định danh duy nhất cho mỗi bản ghi trong một bảng, không được trùng lặp và không được để trống.

Ví dụ:

[MaKhachHang] là khóa chính của bảng [KhachHang]

[MaNhanVien] là khóa chính của bảng [NhanVien]

Khóa ngoại (Foreign Key - FK)

* Khóa ngoại dùng để tạo mối liên kết giữa các bảng, đảm bảo dữ liệu ở bảng con phải tham chiếu đúng tới bảng cha.

Ví dụ:

[HoaDon].[MaKhachHang] tham chiếu đến [KhachHang].[MaKhachHang]

[HoaDon].[MaNhanVien] tham chiếu đến [NhanVien].[MaNhanVien]

Điều này giúp tránh tình trạng tạo hóa đơn cho khách hàng không tồn tại hoặc nhập chi tiết cho sản phẩm không có trong kho.
* Ràng buộc kiểm tra (Check Constraint - CK)

Check Constraint dùng để giới hạn miền giá trị hợp lệ cho một trường dữ liệu, tránh nhập sai logic.

=> Nhờ có các ràng buộc này mà dữ liệu trong cơ sở dữ liệu luôn được kiểm soát chặt chẽ và hạn chế sai sót trong quá trình nhập liệu.
# PHẦN 2: XÂY DỰNG FUNCTION (Kiến thức 8,9)
2.1. Giới thiệu một số Built-in Function có sẵn trong SQL Server (Kiến thức 8)\
SQL Server cung cấp rất nhiều hàm dựng sẵn nhằm hỗ trợ xử lý dữ liệu nhanh chóng. Một số nhóm hàm phổ biến gồm:

* Hàm chuỗi ký tự
LEN(): đếm độ dài chuỗi
UPPER(): chuyển thành chữ hoa
LOWER(): chuyển thành chữ thường
LEFT(), RIGHT(), SUBSTRING(): cắt chuỗi
CONCAT(): nối chuỗi
* Hàm số học
ROUND(): làm tròn
ABS(): trị tuyệt đối
CEILING(), FLOOR(): làm tròn trên/dưới
* Hàm tổng hợp
COUNT(), SUM(), AVG(), MAX(), MIN()
*Một số System Stored Procedure có sẵn
sp_help: xem thông tin đối tượng
sp_helptext: xem mã nguồn function/procedure
sp_rename: đổi tên đối tượng
=> Mặc dù SQL Server có nhiều hàm dựng sẵn, nhưng trong nghiệp vụ quản lý bán hàng vẫn cần tự xây dựng function riêng để đóng gói các phép tính đặc thù như tính thành tiền, thống kê doanh thu hoặc tra cứu sản phẩm.
2.2. Scalar Function - Hàm trả về một giá trị
Bài toán:

Khi bán hàng, mỗi dòng chi tiết hóa đơn cần tính thành tiền theo công thức:

Thành tiền = Số lượng bán × Đơn giá

Để tái sử dụng nhiều lần, ta xây dựng scalar function.

CREATE FUNCTION [dbo].[fn_TinhThanhTien]
(
    @SoLuongBan INT,
    @DonGia DECIMAL(18,2)
)
RETURNS DECIMAL(18,2)
AS
BEGIN
    DECLARE @ThanhTien DECIMAL(18,2);
    SET @ThanhTien = @SoLuongBan * @DonGia;
    RETURN @ThanhTien;
END;
GO
Khai thác Scalar Function
SELECT dbo.[fn_TinhThanhTien](3,450000) AS ThanhTienThu;
GO

Function này có thể dùng lại trong Trigger, Procedure hoặc câu SELECT bất kỳ.

2.3. Inline Table-Valued Function - Hàm trả về bảng dữ liệu dạng inline
Bài toán:

Cần viết hàm trả về danh sách các sản phẩm còn hàng trong kho để nhân viên dễ tra cứu.

```CREATE FUNCTION [dbo].[fn_DanhSachSanPhamConHang]()
RETURNS TABLE
AS
RETURN
(
    SELECT
        [MaSanPham],
        [TenSanPham],
        [DonGia],
        [SoLuongTon]
    FROM [SanPham]
    WHERE [SoLuongTon] > 0 AND [TrangThai] = 1
);
GO
```
Khai thác Inline Function 
```
 SELECT * FROM dbo.[fn_DanhSachSanPhamConHang]();
GO
```
Đây là loại function trả về trực tiếp một câu SELECT nên tốc độ xử lý nhanh và cú pháp gọn.

2.4. Multi-Statement Table-Valued Function - Hàm trả về bảng nhiều bước xử lý
Bài toán:

Thống kê doanh thu bán hàng theo từng khách hàng trong một tháng bất kỳ. Do yêu cầu phải:

gom nhiều hóa đơn,
tính tổng tiền,
đếm số hóa đơn,
xếp hạng khách mua nhiều,

nên cần dùng biến bảng và nhiều bước xử lý bên trong function.
```
CREATE FUNCTION [dbo].[fn_ThongKeDoanhThuKhachHang]
(
    @Thang INT,
    @Nam INT
)
RETURNS @KetQua TABLE
(
    [MaKhachHang] INT,
    [TenKhachHang] NVARCHAR(100),
    [SoHoaDon] INT,
    [TongDoanhThu] DECIMAL(18,2),
    [XepHang] INT
)
AS

BEGIN
    INSERT INTO @KetQua([MaKhachHang],[TenKhachHang],[SoHoaDon],[TongDoanhThu],[XepHang])
    SELECT
        kh.[MaKhachHang],
        kh.[TenKhachHang],
        COUNT(hd.[MaHoaDon]) AS SoHoaDon,
        ISNULL(SUM(hd.[TongTien]),0) AS TongDoanhThu,
        0
    FROM [KhachHang] kh
    LEFT JOIN [HoaDon] hd ON kh.[MaKhachHang] = hd.[MaKhachHang]
        AND MONTH(hd.[NgayLap]) = @Thang
        AND YEAR(hd.[NgayLap]) = @Nam
    GROUP BY kh.[MaKhachHang], kh.[TenKhachHang];


    UPDATE k
    SET k.[XepHang] = x.[Hang]
    FROM @KetQua k
    INNER JOIN
    (
        SELECT [MaKhachHang],
               ROW_NUMBER() OVER(ORDER BY [TongDoanhThu] DESC) AS Hang
        FROM @KetQua
    ) x ON k.[MaKhachHang] = x.[MaKhachHang];


    RETURN;
END;
GO
```
Khai thác Multi-Statement Function
```
SELECT * FROM dbo.[fn_ThongKeDoanhThuKhachHang](5,2025);
GO
```
2.5. Nhận xét phần Function

Qua phần này có thể thấy:

Scalar Function thích hợp cho các phép tính đơn lẻ.
Inline Table-Valued Function thích hợp cho truy vấn trả về bảng đơn giản.
Multi-Statement Table-Valued Function dùng khi cần nhiều bước xử lý logic trước khi trả dữ liệu.

Việc tự xây dựng Function giúp chương trình quản lý bán hàng dễ tái sử dụng mã nguồn, giảm trùng lặp câu lệnh SQL và làm cho hệ thống trở nên chuyên nghiệp hơn.
```
* phần code
-- 2.1 Built-in function demo + system procedure demo
SELECT LEN(N'Quan Ly Ban Hang') AS DoDaiChuoi;
SELECT UPPER(N'nguyen van an') AS ChuHoa;
SELECT ROUND(123456.789,2) AS LamTron;
SELECT DATEDIFF(DAY,'2025-05-01',GETDATE()) AS SoNgay;

EXEC sp_help 'SanPham';
GO
-- 2.2 Scalar Function — fn_TinhThanhTien
CREATE FUNCTION [dbo].[fn_TinhThanhTien]
(
    @SoLuongBan INT,
    @DonGia DECIMAL(18,2)
)
RETURNS DECIMAL(18,2)
AS
BEGIN
    DECLARE @ThanhTien DECIMAL(18,2);
    SET @ThanhTien = @SoLuongBan * @DonGia;
    RETURN @ThanhTien;
END;
GO
-- 2.3 Inline Table-Valued Function — fn_DanhSachSanPhamConHang
CREATE FUNCTION [dbo].[fn_DanhSachSanPhamConHang]()
RETURNS TABLE
AS
RETURN
(
    SELECT
        [MaSanPham],
        [TenSanPham],
        [DonGia],
        [SoLuongTon]
    FROM [SanPham]
    WHERE [SoLuongTon] > 0 AND [TrangThai] = 1
);
GO
-- 2.4 Multi Statement Table-Valued Function — fn_ThongKeDoanhThuKhachHang
CREATE FUNCTION [dbo].[fn_ThongKeDoanhThuKhachHang]
(
    @Thang INT,
    @Nam INT
)
RETURNS @KetQua TABLE
(
    [MaKhachHang] INT,
    [TenKhachHang] NVARCHAR(100),
    [SoHoaDon] INT,
    [TongDoanhThu] DECIMAL(18,2),
    [XepHang] INT
)
AS
BEGIN
    INSERT INTO @KetQua([MaKhachHang],[TenKhachHang],[SoHoaDon],[TongDoanhThu],[XepHang])
    SELECT
        kh.[MaKhachHang],
        kh.[TenKhachHang],
        COUNT(hd.[MaHoaDon]) AS SoHoaDon,
        ISNULL(SUM(hd.[TongTien]),0) AS TongDoanhThu,
        0
    FROM [KhachHang] kh
    LEFT JOIN [HoaDon] hd ON kh.[MaKhachHang] = hd.[MaKhachHang]
        AND MONTH(hd.[NgayLap]) = @Thang
        AND YEAR(hd.[NgayLap]) = @Nam
    GROUP BY kh.[MaKhachHang], kh.[TenKhachHang];

    UPDATE k
    SET k.[XepHang] = x.[Hang]
    FROM @KetQua k
    INNER JOIN
    (
        SELECT [MaKhachHang],
               ROW_NUMBER() OVER(ORDER BY [TongDoanhThu] DESC) AS Hang
        FROM @KetQua
    ) x ON k.[MaKhachHang] = x.[MaKhachHang];

    RETURN;
END;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/f844b034-f16e-49a0-9278-e13e2d7d1abd" />

#Phần 3: Xây dựng Store Procedure (Kiến thức 10)

```
--3.1 SP_ThemKhachHang
IF OBJECT_ID('dbo.sp_ThemKhachHang','P') IS NOT NULL
    DROP PROCEDURE dbo.sp_ThemKhachHang;
GO

CREATE PROCEDURE [dbo].[sp_ThemKhachHang]
    @TenKhachHang NVARCHAR(100),
    @SoDienThoai VARCHAR(15),
    @DiaChi NVARCHAR(200)
AS
BEGIN
    SET NOCOUNT ON;

    IF EXISTS (SELECT 1 FROM [KhachHang] WHERE [SoDienThoai] = @SoDienThoai)
    BEGIN
        RAISERROR(N'Số điện thoại đã tồn tại.',16,1);
        RETURN;
    END

    INSERT INTO [KhachHang]([TenKhachHang],[SoDienThoai],[DiaChi])
    VALUES (@TenKhachHang,@SoDienThoai,@DiaChi);

    PRINT N'Thêm khách hàng thành công.';
END;
GO
--3.2 SP_TaoHoaDon
IF OBJECT_ID('dbo.sp_TaoHoaDon','P') IS NOT NULL
    DROP PROCEDURE dbo.sp_TaoHoaDon;
GO

CREATE PROCEDURE [dbo].[sp_TaoHoaDon]
    @MaKhachHang INT,
    @MaNhanVien INT,
    @MaHoaDonMoi INT OUTPUT,
    @TongSoHoaDonKhach INT OUTPUT
AS
BEGIN
    SET NOCOUNT ON;

    INSERT INTO [HoaDon]([MaKhachHang],[MaNhanVien],[NgayLap])
    VALUES (@MaKhachHang,@MaNhanVien,CAST(GETDATE() AS DATE));

    SET @MaHoaDonMoi = SCOPE_IDENTITY();

    SELECT @TongSoHoaDonKhach = COUNT(*)
    FROM [HoaDon]
    WHERE [MaKhachHang] = @MaKhachHang;
END;
GO
--3.3 SP_BaoCaoHoaDon
IF OBJECT_ID('dbo.sp_BaoCaoHoaDon','P') IS NOT NULL
    DROP PROCEDURE dbo.sp_BaoCaoHoaDon;
GO

CREATE PROCEDURE [dbo].[sp_BaoCaoHoaDon]
    @TuNgay DATE = NULL,
    @DenNgay DATE = NULL
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        hd.[MaHoaDon],
        kh.[TenKhachHang],
        nv.[TenNhanVien],
        hd.[NgayLap],
        hd.[TongTien],
        COUNT(ct.[MaChiTiet]) AS SoMatHang,
        SUM(ct.[SoLuongBan]) AS TongSoLuong,
        STRING_AGG(sp.[TenSanPham], N' | ') AS DanhSachSanPham
    FROM [HoaDon] hd
    INNER JOIN [KhachHang] kh ON hd.[MaKhachHang] = kh.[MaKhachHang]
    INNER JOIN [NhanVien] nv ON hd.[MaNhanVien] = nv.[MaNhanVien]
    LEFT JOIN [ChiTietHoaDon] ct ON hd.[MaHoaDon] = ct.[MaHoaDon]
    LEFT JOIN [SanPham] sp ON ct.[MaSanPham] = sp.[MaSanPham]
    WHERE (@TuNgay IS NULL OR hd.[NgayLap] >= @TuNgay)
      AND (@DenNgay IS NULL OR hd.[NgayLap] <= @DenNgay)
    GROUP BY
        hd.[MaHoaDon],
        kh.[TenKhachHang],
        nv.[TenNhanVien],
        hd.[NgayLap],
        hd.[TongTien]
    ORDER BY hd.[NgayLap] DESC;
END;
GO
```
<img width="1910" height="1079" alt="image" src="https://github.com/user-attachments/assets/e3930cb1-18ab-4a1c-a5b2-e08f50d8ac1f" />
* CHÚ THÍCH / TƯỜNG THUẬT TỪNG STORE PROCEDURE

1. sp_ThemKhachHang — loại procedure không trả dữ liệu

Procedure này dùng để:

thêm một khách hàng mới vào hệ thống.

Trước khi thêm, chương trình kiểm tra:

nếu số điện thoại đã tồn tại thì báo lỗi.

Điều này giúp:

tránh nhập trùng khách hàng.

Store procedure này chỉ thực hiện thao tác INSERT và PRINT thông báo, không trả dữ liệu ra ngoài nên thuộc nhóm:

procedure không trả về dữ liệu.

2. sp_TaoHoaDon — loại procedure trả dữ liệu qua OUTPUT

Procedure này dùng để:

tạo một hóa đơn bán hàng mới.

Sau khi insert hóa đơn:

hệ thống lấy mã hóa đơn vừa tạo bằng SCOPE_IDENTITY(),
đồng thời đếm tổng số hóa đơn mà khách hàng đó đã mua.

Hai giá trị này được trả ra ngoài thông qua:

OUTPUT

tham số.

Nên đây là:

procedure trả giá trị thông qua tham số output.

3. sp_BaoCaoHoaDon — loại procedure trả về tập dữ liệu SELECT

Procedure này dùng để:

in báo cáo danh sách hóa đơn trong khoảng ngày tùy chọn.

Bên trong có:

JOIN KhachHang
JOIN NhanVien
JOIN ChiTietHoaDon
JOIN SanPham

sau đó:

đếm số mặt hàng,
tính tổng số lượng,
gom tên sản phẩm bằng STRING_AGG.

Khi thực thi:

EXEC dbo.[sp_BaoCaoHoaDon]

SQL Server sẽ trả ra nguyên một bảng kết quả.

Đây chính là:

store procedure trả về dữ liệu của lệnh SELECT bên trong procedure.


#PHẦN 4 — TRIGGER + CURSOR (BẢN CHẠY ỔN)
Trong hệ thống quản lý bán hàng, dữ liệu giữa các bảng luôn có mối liên hệ chặt chẽ với nhau. Khi thông tin tại một bảng thay đổi thì các bảng liên quan cũng cần được cập nhật tương ứng để đảm bảo tính đồng bộ và toàn vẹn dữ liệu.

Vì vậy, trong phần này em sử dụng TRIGGER để tự động xử lý nghiệp vụ khi dữ liệu phát sinh thay đổi.

Trigger là một đoạn chương trình SQL đặc biệt, được SQL Server tự động kích hoạt khi trên bảng xảy ra thao tác INSERT, UPDATE hoặc DELETE.

Trong đề tài này, em xây dựng mối liên hệ giữa:

Bảng A: [ChiTietHoaDon] — lưu chi tiết các sản phẩm được bán trong từng hóa đơn.
Bảng B: [HoaDon] — lưu thông tin tổng quát của hóa đơn như ngày lập, khách hàng, tổng tiền.
Bảng C: [SanPham] — lưu số lượng tồn kho của từng mặt hàng.

Khi bảng [ChiTietHoaDon] thay đổi thì dữ liệu tại [HoaDon] và [SanPham] cũng phải tự động thay đổi theo.
```
*Phần code:
-- 4.1 Trigger Insert
IF OBJECT_ID('dbo.trg_ChiTietHoaDon_Insert','TR') IS NOT NULL
    DROP TRIGGER dbo.trg_ChiTietHoaDon_Insert;
GO

CREATE TRIGGER [dbo].[trg_ChiTietHoaDon_Insert]
ON [ChiTietHoaDon]
AFTER INSERT
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @MaSanPham INT;
    DECLARE @SoLuongBan INT;
    DECLARE @MaHoaDon INT;
    DECLARE @DonGia DECIMAL(18,2);

    DECLARE cur_Insert CURSOR FOR
        SELECT i.[MaSanPham], i.[SoLuongBan], i.[MaHoaDon], sp.[DonGia]
        FROM inserted i
        INNER JOIN [SanPham] sp ON i.[MaSanPham] = sp.[MaSanPham];

    OPEN cur_Insert;
    FETCH NEXT FROM cur_Insert INTO @MaSanPham,@SoLuongBan,@MaHoaDon,@DonGia;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        IF NOT EXISTS
        (
            SELECT 1 FROM [SanPham]
            WHERE [MaSanPham]=@MaSanPham
            AND [SoLuongTon] >= @SoLuongBan
        )
        BEGIN
            RAISERROR(N'Sản phẩm mã %d không đủ tồn kho.',16,1,@MaSanPham);
            ROLLBACK TRANSACTION;
            CLOSE cur_Insert;
            DEALLOCATE cur_Insert;
            RETURN;
        END

        UPDATE [SanPham]
        SET [SoLuongTon] = [SoLuongTon] - @SoLuongBan
        WHERE [MaSanPham] = @MaSanPham;

        UPDATE [HoaDon]
        SET [TongTien] = [TongTien] + dbo.[fn_TinhThanhTien](@SoLuongBan,@DonGia)
        WHERE [MaHoaDon] = @MaHoaDon;

        FETCH NEXT FROM cur_Insert INTO @MaSanPham,@SoLuongBan,@MaHoaDon,@DonGia;
    END

    CLOSE cur_Insert;
    DEALLOCATE cur_Insert;
END;
GO
--4.2 Trigger Delete
IF OBJECT_ID('dbo.trg_ChiTietHoaDon_Delete','TR') IS NOT NULL
    DROP TRIGGER dbo.trg_ChiTietHoaDon_Delete;
GO

CREATE TRIGGER [dbo].[trg_ChiTietHoaDon_Delete]
ON [ChiTietHoaDon]
AFTER DELETE
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @MaSanPham INT;
    DECLARE @SoLuongBan INT;
    DECLARE @MaHoaDon INT;
    DECLARE @DonGia DECIMAL(18,2);

    DECLARE cur_Delete CURSOR FOR
        SELECT d.[MaSanPham], d.[SoLuongBan], d.[MaHoaDon], sp.[DonGia]
        FROM deleted d
        INNER JOIN [SanPham] sp ON d.[MaSanPham] = sp.[MaSanPham];

    OPEN cur_Delete;
    FETCH NEXT FROM cur_Delete INTO @MaSanPham,@SoLuongBan,@MaHoaDon,@DonGia;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        UPDATE [SanPham]
        SET [SoLuongTon] = [SoLuongTon] + @SoLuongBan
        WHERE [MaSanPham] = @MaSanPham;

        UPDATE [HoaDon]
        SET [TongTien] = [TongTien] - dbo.[fn_TinhThanhTien](@SoLuongBan,@DonGia)
        WHERE [MaHoaDon] = @MaHoaDon;

        FETCH NEXT FROM cur_Delete INTO @MaSanPham,@SoLuongBan,@MaHoaDon,@DonGia;
    END

    CLOSE cur_Delete;
    DEALLOCATE cur_Delete;
END;
GO
-- 4.3 Trigger Update
IF OBJECT_ID('dbo.trg_ChiTietHoaDon_Update','TR') IS NOT NULL
    DROP TRIGGER dbo.trg_ChiTietHoaDon_Update;
GO

CREATE TRIGGER [dbo].[trg_ChiTietHoaDon_Update]
ON [ChiTietHoaDon]
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    UPDATE hd
    SET [TongTien] =
    (
        SELECT SUM(dbo.[fn_TinhThanhTien](ct.[SoLuongBan],sp.[DonGia]))
        FROM [ChiTietHoaDon] ct
        INNER JOIN [SanPham] sp ON ct.[MaSanPham] = sp.[MaSanPham]
        WHERE ct.[MaHoaDon] = hd.[MaHoaDon]
    )
    FROM [HoaDon] hd
    WHERE hd.[MaHoaDon] IN (SELECT DISTINCT [MaHoaDon] FROM inserted);
END;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/da009566-655e-497a-9ec5-05df0b77377a" />
* 4.1 Trigger khi thêm dữ liệu vào bảng ChiTietHoaDon

Khi nhân viên bán hàng thêm một sản phẩm vào chi tiết hóa đơn, hệ thống cần tự động thực hiện hai công việc:

Giảm số lượng tồn kho của sản phẩm tương ứng trong bảng [SanPham].
Cập nhật tăng TongTien của hóa đơn tương ứng trong bảng [HoaDon].

Do một câu lệnh INSERT có thể thêm nhiều dòng dữ liệu cùng lúc nên em sử dụng CURSOR bên trong Trigger để duyệt từng bản ghi mới được thêm vào bảng inserted.

Với mỗi dòng dữ liệu:

hệ thống lấy ra MaSanPham,
số lượng bán SoLuongBan,
mã hóa đơn MaHoaDon.

Sau đó Trigger kiểm tra:

nếu số lượng tồn kho hiện tại nhỏ hơn số lượng bán thì hệ thống báo lỗi và thực hiện ROLLBACK TRANSACTION để hủy toàn bộ giao dịch,
nếu tồn kho đủ thì trừ kho và cộng tiền vào hóa đơn.

Việc sử dụng Trigger giúp cho người dùng chỉ cần nhập dữ liệu ở bảng ChiTietHoaDon, còn việc cập nhật kho và tổng tiền sẽ được hệ thống xử lý hoàn toàn tự động.

* 4.2 Trigger khi xóa dữ liệu khỏi bảng ChiTietHoaDon

Trong trường hợp người dùng xóa một dòng chi tiết hóa đơn (ví dụ nhập nhầm sản phẩm hoặc hủy mặt hàng), hệ thống cũng phải tự động xử lý ngược lại:

cộng trả lại số lượng hàng vào kho,
trừ bớt tiền khỏi tổng hóa đơn.

Trigger AFTER DELETE được xây dựng để giải quyết bài toán này.

SQL Server lưu các dòng vừa bị xóa vào bảng tạm deleted, từ đó Trigger đọc từng dòng dữ liệu:

xác định sản phẩm nào bị xóa,
số lượng bao nhiêu,
thuộc hóa đơn nào.

Sau đó:

cộng số lượng vừa xóa vào SoLuongTon của bảng [SanPham],
đồng thời giảm TongTien ở bảng [HoaDon].

Nhờ đó dữ liệu giữa các bảng luôn nhất quán, tránh tình trạng xóa chi tiết nhưng kho hoặc tổng tiền không thay đổi.

* 4.3 Trigger khi cập nhật dữ liệu tại bảng ChiTietHoaDon

Ngoài việc thêm và xóa, khi người dùng sửa lại số lượng bán trong một dòng chi tiết hóa đơn thì tổng tiền của hóa đơn cũng phải được tính toán lại.

Vì vậy em xây dựng Trigger AFTER UPDATE trên bảng [ChiTietHoaDon].

Khi Trigger này được kích hoạt, hệ thống sẽ:

lấy toàn bộ các dòng chi tiết thuộc hóa đơn vừa bị thay đổi,
nối với bảng [SanPham] để lấy DonGia,
sau đó tính lại tổng tiền mới của hóa đơn bằng cách cộng tất cả thành tiền của từng dòng.

Việc tính lại toàn bộ giúp cho kết quả chính xác tuyệt đối kể cả khi người dùng sửa tăng hoặc giảm số lượng ở nhiều sản phẩm khác nhau.
* 4.4 Thử nghiệm Trigger hai chiều giữa hai bảng

Để quan sát sâu hơn về cơ chế hoạt động của Trigger, em tiến hành xây dựng thêm một Trigger trên bảng [HoaDon].

Cụ thể:

khi TongTien trong bảng [HoaDon] bị cập nhật,
Trigger này sẽ tiếp tục cập nhật ngược lại bảng [ChiTietHoaDon].

Như vậy hệ thống hình thành mô hình:

bảng [ChiTietHoaDon] cập nhật → Trigger làm thay đổi [HoaDon],
bảng [HoaDon] cập nhật → Trigger tiếp tục làm thay đổi [ChiTietHoaDon].

Sau khi thực hiện lệnh cập nhật dữ liệu thử nghiệm, SQL Server xuất hiện liên tiếp nhiều thông báo ở cửa sổ Messages.

Nguyên nhân là vì:

Trigger của bảng ChiTietHoaDon chạy trước,
nó cập nhật bảng HoaDon,
khi bảng HoaDon thay đổi thì Trigger gắn trên HoaDon lại tiếp tục chạy,
Trigger của HoaDon lại cập nhật ngược về ChiTietHoaDon,
làm cho Trigger ở ChiTietHoaDon tiếp tục được gọi thêm lần nữa.

Quá trình này lặp đi lặp lại tạo thành một chuỗi Trigger gọi lẫn nhau.

Hiện tượng này trong SQL Server được gọi là:

Nested Trigger hoặc Recursive Trigger (Trigger lồng nhau / Trigger đệ quy).
```
* Phần code:
IF OBJECT_ID('dbo.trg_HoaDon_UpdateNguoc','TR') IS NOT NULL
    DROP TRIGGER dbo.trg_HoaDon_UpdateNguoc;
GO

CREATE TRIGGER [dbo].[trg_HoaDon_UpdateNguoc]
ON [HoaDon]
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    PRINT N'Trigger B đang chạy: HoaDon cập nhật ngược lại ChiTietHoaDon';

    UPDATE ct
    SET [SoLuongBan] = [SoLuongBan]
    FROM [ChiTietHoaDon] ct
    INNER JOIN inserted i ON ct.[MaHoaDon] = i.[MaHoaDon];
END;
GO

```
<img width="553" height="311" alt="image" src="https://github.com/user-attachments/assets/c2fd62b1-724a-4e2b-828e-31156fe3c9b0" />

* 4.5 Giải thích thông báo hệ thống

Trong quá trình thử nghiệm, cửa sổ Messages của SQL Server liên tục hiển thị các dòng thông báo Trigger đang thực thi.

Điều này chứng tỏ SQL Server không phân biệt việc dữ liệu bị cập nhật bởi người dùng hay bị cập nhật bởi Trigger khác.

Chỉ cần dữ liệu của bảng thay đổi thì Trigger trên bảng đó sẽ tự động kích hoạt.

Do đó khi hai Trigger cập nhật qua lại giữa hai bảng, hệ thống sẽ tạo thành một chu trình xử lý liên hoàn.

Nếu không có điều kiện kiểm soát, việc này có thể dẫn đến:

số lần Trigger chạy quá nhiều,
làm chậm tốc độ xử lý,
tiêu hao tài nguyên hệ thống,
thậm chí có thể phát sinh lỗi vượt mức Trigger lồng nhau.
```
UPDATE [ChiTietHoaDon]
SET [SoLuongBan] = [SoLuongBan] + 1
WHERE [MaChiTiet] = 1;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/2f8a595e-941f-4fdb-903e-93cdd01ed9b9" />
4.6 Nhận xét 

Qua phần thực nghiệm có thể thấy rằng Trigger là công cụ rất mạnh trong SQL Server, giúp tự động đồng bộ dữ liệu giữa các bảng mà không cần người dùng thao tác thủ công.

Nhờ Trigger, hệ thống quản lý bán hàng có thể:

tự trừ tồn kho,
tự cập nhật tổng tiền hóa đơn,
tự đảm bảo tính nhất quán của dữ liệu.

Tuy nhiên, khi thiết kế Trigger theo hai chiều giữa các bảng mà không có điều kiện chặn, hệ thống sẽ phát sinh hiện tượng Trigger lồng nhau, dẫn đến xử lý lặp lại nhiều lần và gây ảnh hưởng xấu đến hiệu năng.

Vì vậy trong các hệ thống thực tế, Trigger thường chỉ nên được thiết kế theo một chiều hoặc phải có thêm cơ chế kiểm soát để tránh vòng lặp đệ quy.

# PHẦN 5. SỬ DỤNG CURSOR ĐỂ DUYỆT VÀ XỬ LÝ DỮ LIỆU TRONG SQL SERVER

Trong SQL Server, ngoài cách xử lý dữ liệu theo tập hợp (set-based), hệ quản trị còn cung cấp cơ chế CURSOR dùng để duyệt từng bản ghi một cách tuần tự.

CURSOR thường được sử dụng trong những bài toán mà:

mỗi dòng dữ liệu cần xử lý theo logic riêng,
cần kiểm tra điều kiện IF...ELSE khác nhau cho từng bản ghi,
hoặc cần thực hiện các thao tác lặp mà câu lệnh UPDATE thông thường khó biểu diễn.

Trong đề tài quản lý bán hàng, em xây dựng một bài toán thực tế như sau:

Kiểm tra tất cả các sản phẩm trong kho.
Nếu sản phẩm nào có SoLuongTon nhỏ hơn 10 thì hệ thống sẽ tự động tăng giá bán thêm 5% nhằm hạn chế khách mua quá nhiều khi hàng sắp hết.

Đây là một tình huống mang tính nghiệp vụ thực tế vì trong kinh doanh, những mặt hàng sắp hết đôi khi sẽ được điều chỉnh giá hoặc gắn cờ cảnh báo.
* 5.1 Dùng CURSOR duyệt từng sản phẩm và tăng giá nếu tồn kho thấp
```
DECLARE @MaSanPham INT;
DECLARE @SoLuongTon INT;
DECLARE @DonGia DECIMAL(18,2);

DECLARE cur_KiemTraKho CURSOR FOR
    SELECT [MaSanPham], [SoLuongTon], [DonGia]
    FROM [SanPham];

OPEN cur_KiemTraKho;
FETCH NEXT FROM cur_KiemTraKho INTO @MaSanPham, @SoLuongTon, @DonGia;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @SoLuongTon < 10
    BEGIN
        UPDATE [SanPham]
        SET [DonGia] = [DonGia] * 1.05
        WHERE [MaSanPham] = @MaSanPham;

        PRINT N'San pham ma ' + CAST(@MaSanPham AS VARCHAR) + N' ton kho thap, da tang gia 5%';
    END

    FETCH NEXT FROM cur_KiemTraKho INTO @MaSanPham, @SoLuongTon, @DonGia;
END

CLOSE cur_KiemTraKho;
DEALLOCATE cur_KiemTraKho;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/7eff03b2-d367-4466-9df8-ca532cf4d4c2" />

*Giải quyết bài toán bằng CURSOR

Đầu tiên, em sử dụng CURSOR để duyệt lần lượt từng sản phẩm trong bảng [SanPham].

CURSOR sẽ lấy ra:

MaSanPham
SoLuongTon
DonGia

sau đó SQL Server mở con trỏ và đọc từng bản ghi một.

Với mỗi sản phẩm:

nếu SoLuongTon < 10 thì thực hiện UPDATE tăng DonGia lên 5%,
nếu không thì bỏ qua.

Quá trình này được thực hiện trong vòng lặp WHILE @@FETCH_STATUS = 0, nghĩa là hệ thống sẽ xử lý tuần tự cho đến khi hết dữ liệu.

Ưu điểm của cách này là:

dễ chèn thêm nhiều điều kiện phức tạp,
có thể PRINT thông báo riêng cho từng sản phẩm,
rất dễ mô phỏng luồng xử lý nghiệp vụ.
* 5.2 Giải quyết cùng bài toán nhưng không dùng CURSOR

Sau khi hoàn thành bằng CURSOR, em tiếp tục viết lại cùng một bài toán bằng cách xử lý theo tập dữ liệu:

chỉ cần một câu lệnh:

UPDATE [SanPham]
kết hợp WHERE [SoLuongTon] < 10

thì SQL Server sẽ tự động cập nhật toàn bộ các sản phẩm thỏa điều kiện trong một lần xử lý.

Cách này không cần:

khai báo con trỏ,
không cần OPEN, FETCH, WHILE, CLOSE, DEALLOCATE.

Toàn bộ dữ liệu được hệ quản trị tối ưu xử lý theo dạng batch.
```
UPDATE [SanPham]
SET [DonGia] = [DonGia] * 1.05
WHERE [SoLuongTon] < 10;
GO
```
*kiểm tra kết quả sau khi chạy cursor
```
SELECT [MaSanPham],[TenSanPham],[SoLuongTon],[DonGia]
FROM [SanPham];
GO
```
<img width="1919" height="1078" alt="image" src="https://github.com/user-attachments/assets/2f2dc82a-762b-4821-a396-91ca73ccd70b" />

* 5.3 So sánh tốc độ giữa CURSOR và không dùng CURSOR

Để so sánh, em sử dụng:

SET STATISTICS TIME ON;

trước mỗi đoạn lệnh.

Kết quả thử nghiệm cho thấy:

đoạn CURSOR mất nhiều thời gian CPU hơn do phải duyệt từng hàng,
mỗi lần đọc một bản ghi SQL Server đều phải thực hiện thao tác FETCH,
đồng thời phải duy trì trạng thái mở của con trỏ trong bộ nhớ.

Trong khi đó:

câu lệnh UPDATE không dùng CURSOR chỉ cần SQL Server quét bảng một lần,
tối ưu toàn bộ kế hoạch thực thi trong một batch duy nhất.

Do đó tốc độ xử lý nhanh hơn rõ rệt.
* Đo thời gian xử lý để so sánh
đo CURSOR
```
SET STATISTICS TIME ON;

DECLARE @MaSP INT;
DECLARE @SLTon INT;
DECLARE @Gia DECIMAL(18,2);

DECLARE cur_Test CURSOR FOR
    SELECT [MaSanPham], [SoLuongTon], [DonGia]
    FROM [SanPham];

OPEN cur_Test;
FETCH NEXT FROM cur_Test INTO @MaSP, @SLTon, @Gia;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @SLTon < 10
    BEGIN
        UPDATE [SanPham]
        SET [DonGia] = [DonGia]
        WHERE [MaSanPham] = @MaSP;
    END

    FETCH NEXT FROM cur_Test INTO @MaSP, @SLTon, @Gia;
END

CLOSE cur_Test;
DEALLOCATE cur_Test;

SET STATISTICS TIME OFF;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/132214c7-a617-4c48-b490-c89b307a0f7d" />

*đo không dùng CURSOR
```
SET STATISTICS TIME ON;

UPDATE [SanPham]
SET [DonGia] = [DonGia]
WHERE [SoLuongTon] < 10;

SET STATISTICS TIME OFF;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/add58704-300d-49f1-977b-8cd2e0062386" />
* 5.4 Nhận xét về việc sử dụng CURSOR

Qua thử nghiệm em nhận thấy rằng CURSOR tuy chậm hơn nhưng vẫn rất hữu ích trong các bài toán sau:

cần xử lý logic riêng cho từng dòng,
cần in thông báo hoặc ghi log từng bản ghi,
cần gọi Function hoặc Stored Procedure với tham số khác nhau trên mỗi dòng dữ liệu,
cần rẽ nhánh IF...ELSE phức tạp.

Ngược lại, với các bài toán chỉ đơn thuần cập nhật hàng loạt dữ liệu giống nhau thì nên ưu tiên viết theo kiểu không dùng CURSOR để đạt hiệu năng cao hơn.

Như vậy:

CURSOR không phải lúc nào cũng tối ưu về tốc độ,
nhưng là công cụ rất cần thiết khi xử lý nghiệp vụ tuần tự và chi tiết trong SQL Server.





















