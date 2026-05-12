* Tên : Nguyễn Hữu Văn Tuân

* Mssv : K235480106075

* Lớp : K59KMT.K01


* Sau đây là phần bài làm của em


# Phần 1 : Thiết kế CSDL
```
USE master;
GO

-- Ngắt kết nối rồi mới drop
ALTER DATABASE QuanLyCamDo SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
GO

DROP DATABASE IF EXISTS QuanLyCamDo;
GO

CREATE DATABASE QuanLyCamDo COLLATE Vietnamese_CI_AS;
GO

USE QuanLyCamDo;
GO

-- ============================================================
-- PHẦN 1: TẠO BẢNG (DDL)
-- ============================================================

-- -------------------------------------------------------
-- 1.1 Bảng KhachHang
-- -------------------------------------------------------
CREATE TABLE KhachHang (
    MaKH        INT           IDENTITY(1,1) PRIMARY KEY,
    HoTen       NVARCHAR(100) NOT NULL,
    SDT         VARCHAR(15)   NOT NULL UNIQUE,
    CCCD        VARCHAR(20)   NOT NULL UNIQUE,
    DiaChi      NVARCHAR(255),
    NgayTao     DATETIME      NOT NULL DEFAULT GETDATE(),
    GhiChu      NVARCHAR(500)
);
GO

-- -------------------------------------------------------
-- 1.2 Bảng TaiSan
-- -------------------------------------------------------
CREATE TABLE TaiSan (
    MaTS            INT           IDENTITY(1,1) PRIMARY KEY,
    TenTaiSan       NVARCHAR(200) NOT NULL,
    MoTa            NVARCHAR(500),
    GiaTriDinhGia   DECIMAL(18,2) NOT NULL DEFAULT 0,
    -- TrangThai: SanSang | DangCam | ThanhLy | DaTra
    TrangThai       NVARCHAR(30)  NOT NULL DEFAULT N'SanSang'
        CONSTRAINT CHK_TaiSan_TrangThai
        CHECK (TrangThai IN (N'SanSang', N'DangCam', N'ThanhLy', N'DaTra')),
    NgayTao         DATETIME      NOT NULL DEFAULT GETDATE()
);
GO

-- -------------------------------------------------------
-- 1.3 Bảng HopDong
-- -------------------------------------------------------
CREATE TABLE HopDong (
    MaHD        INT           IDENTITY(1,1) PRIMARY KEY,
    MaKH        INT           NOT NULL
        CONSTRAINT FK_HopDong_KhachHang FOREIGN KEY REFERENCES KhachHang(MaKH),
    NgayVay     DATE          NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    TienGoc     DECIMAL(18,2) NOT NULL CHECK (TienGoc > 0),
    -- Lãi đơn: 5000 / 1.000.000 / ngày = 0.5% / ngày
    LaiSuatNgay DECIMAL(10,6) NOT NULL DEFAULT 0.005,
    Deadline1   DATE          NOT NULL,   -- Bắt đầu tính lãi kép
    Deadline2   DATE          NOT NULL,   -- Quá hạn → thanh lý
    -- TrangThai: DangVay | QuaHan | DongHD | ThanhLy
    TrangThai   NVARCHAR(20)  NOT NULL DEFAULT N'DangVay'
        CONSTRAINT CHK_HopDong_TrangThai
        CHECK (TrangThai IN (N'DangVay', N'QuaHan', N'DongHD', N'ThanhLy')),
    TongDaTra   DECIMAL(18,2) NOT NULL DEFAULT 0,
    NgayDongHD  DATE          NULL,
    GhiChu      NVARCHAR(500),
    CONSTRAINT CHK_HopDong_Deadline CHECK (Deadline1 < Deadline2 AND NgayVay < Deadline1)
);
GO

-- -------------------------------------------------------
-- 1.4 Bảng trung gian HopDong_TaiSan
-- -------------------------------------------------------
CREATE TABLE HopDong_TaiSan (
    MaHD    INT NOT NULL
        CONSTRAINT FK_HDTS_HopDong FOREIGN KEY REFERENCES HopDong(MaHD),
    MaTS    INT NOT NULL
        CONSTRAINT FK_HDTS_TaiSan  FOREIGN KEY REFERENCES TaiSan(MaTS),
    CONSTRAINT PK_HopDong_TaiSan PRIMARY KEY (MaHD, MaTS)
);
GO

-- -------------------------------------------------------
-- 1.5 Bảng ThanhToan
-- -------------------------------------------------------
CREATE TABLE ThanhToan (
    MaTT            INT           IDENTITY(1,1) PRIMARY KEY,
    MaHD            INT           NOT NULL
        CONSTRAINT FK_ThanhToan_HopDong FOREIGN KEY REFERENCES HopDong(MaHD),
    NgayThanhToan   DATE          NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    SoTien          DECIMAL(18,2) NOT NULL CHECK (SoTien > 0),
    GhiChu          NVARCHAR(500)
);
GO

-- -------------------------------------------------------
-- 1.6 Bảng LichSuLog
-- -------------------------------------------------------
CREATE TABLE LichSuLog (
    MaLog       INT           IDENTITY(1,1) PRIMARY KEY,
    MaHD        INT           NULL
        CONSTRAINT FK_Log_HopDong FOREIGN KEY REFERENCES HopDong(MaHD),
    ThoiGian    DATETIME      NOT NULL DEFAULT GETDATE(),
    HanhDong    NVARCHAR(100) NOT NULL,
    NoiDung     NVARCHAR(MAX)
);
GO
```
<img width="553" height="311" alt="image" src="https://github.com/user-attachments/assets/0a72b746-1474-4ba3-a16e-19b01a0925ce" />

Để thực hiện Nhiệm vụ 1, em đã khởi tạo cơ sở dữ liệu QuanLyCamDo và thiết lập 5 bảng dữ liệu chuẩn hóa 3NF gồm: Khách hàng, Nhân viên, Hợp đồng, Tài sản và Log biến động. Các bảng được kết nối chặt chẽ bằng khóa ngoại để quản lý chi tiết từ thông tin người vay đến lịch sử dòng tiền và trạng thái tài sản thế chấp theo đúng yêu cầu nghiệp vụ.


* Tiếp theo em sẽ tạo 1 Database Diagrams
  
<img width="1916" height="1016" alt="image" src="https://github.com/user-attachments/assets/c6b9a7f5-6679-4688-83c3-fb76e2389c83" />
Dựa trên cấu trúc bảng đã cài đặt, em đã tiến hành thiết lập các mối quan hệ (Relationships) trực quan thông qua công cụ Database Diagram để hiện thực hóa quy trình nghiệp vụ. Trong đó, bảng HopDong đóng vai trò trung tâm, được kết nối với bảng KhachHang để xác định chủ khoản vay và bảng TaiSan để quản lý danh mục đồ cầm cố của từng hợp đồng. Đồng thời, các bảng NhanVien và LogBienDong cũng được liên kết chặt chẽ nhằm đảm bảo mọi biến động về dòng tiền và người thực hiện giao dịch đều được lưu vết đầy đủ, phục vụ cho việc kiểm soát nợ thời gian thực.

# Phần 2 : Cài đặt SQL (Yêu cầu viết Scripts)
*Event 1 : Đăng ký hợp đồng mới (Vay tiền)
```
CREATE OR ALTER PROCEDURE sp_TaoHopDong
    @MaKH INT,
    @TienGoc DECIMAL(18,2),
    @SoNgayHanDon INT = 30,
    @SoNgayHanKep INT = 60,
    @DanhSachMaTS NVARCHAR(MAX),
    @GhiChu NVARCHAR(500) = NULL,
    @MaHD_Out INT OUTPUT
AS
BEGIN

    SET NOCOUNT ON;

    BEGIN TRANSACTION;

    BEGIN TRY

        ------------------------------------------------
        -- Kiểm tra khách hàng tồn tại
        ------------------------------------------------

        IF NOT EXISTS (
            SELECT 1
            FROM KhachHang
            WHERE MaKH = @MaKH
        )
        THROW 50001,
        N'Khách hàng không tồn tại.',
        1;

        ------------------------------------------------
        -- Kiểm tra tài sản đã cầm chưa
        ------------------------------------------------

        IF EXISTS (
            SELECT 1
            FROM TaiSan
            WHERE MaTS IN (
                SELECT CAST(value AS INT)
                FROM STRING_SPLIT(
                    @DanhSachMaTS,
                    ','
                )
            )
            AND TrangThai <> N'SanSang'
        )
        THROW 50002,
        N'Tài sản đã được cầm.',
        1;

        ------------------------------------------------
        -- Kiểm tra hạn mức vay
        ------------------------------------------------

        DECLARE @TongGiaTriTS DECIMAL(18,2);

        SELECT
            @TongGiaTriTS =
                SUM(GiaTriDinhGia)
        FROM TaiSan
        WHERE MaTS IN (
            SELECT CAST(value AS INT)
            FROM STRING_SPLIT(
                @DanhSachMaTS,
                ','
            )
        );

        IF @TienGoc > @TongGiaTriTS * 0.7
        THROW 50003,
        N'Số tiền vay vượt 70% giá trị tài sản.',
        1;

        ------------------------------------------------
        -- Thiết lập ngày vay và deadline
        ------------------------------------------------

        DECLARE @NgayVay DATE =
            CAST(GETDATE() AS DATE);

        DECLARE @Deadline1 DATE =
            DATEADD(
                DAY,
                @SoNgayHanDon,
                @NgayVay
            );

        DECLARE @Deadline2 DATE =
            DATEADD(
                DAY,
                @SoNgayHanKep,
                @NgayVay
            );

        ------------------------------------------------
        -- Tạo hợp đồng
        ------------------------------------------------

        INSERT INTO HopDong
        (
            MaKH,
            NgayVay,
            TienGoc,
            Deadline1,
            Deadline2,
            GhiChu
        )
        VALUES
        (
            @MaKH,
            @NgayVay,
            @TienGoc,
            @Deadline1,
            @Deadline2,
            @GhiChu
        );

        SET @MaHD_Out = SCOPE_IDENTITY();

        ------------------------------------------------
        -- Gắn tài sản vào hợp đồng
        ------------------------------------------------

        INSERT INTO HopDong_TaiSan
        (
            MaHD,
            MaTS
        )
        SELECT
            @MaHD_Out,
            CAST(value AS INT)
        FROM STRING_SPLIT(
            @DanhSachMaTS,
            ','
        );

        ------------------------------------------------
        -- Cập nhật trạng thái tài sản
        ------------------------------------------------

        UPDATE TaiSan
        SET TrangThai = N'DangCam'
        WHERE MaTS IN (
            SELECT CAST(value AS INT)
            FROM STRING_SPLIT(
                @DanhSachMaTS,
                ','
            )
        );

        ------------------------------------------------
        -- Ghi log
        ------------------------------------------------

        INSERT INTO LichSuLog
        (
            MaHD,
            HanhDong,
            NoiDung
        )
        VALUES
        (
            @MaHD_Out,
            N'TaoHopDong',
            N'Tạo hợp đồng mới.'
        );

        COMMIT;

    END TRY

    BEGIN CATCH

        ROLLBACK;
        THROW;

    END CATCH

END;
GO
```
<img width="1919" height="1077" alt="image" src="https://github.com/user-attachments/assets/3c4ae7ac-3806-4630-91ff-7a9c9d99b5e5" />
Em đã xây dựng Store Procedure tiếp nhận hợp đồng để tự động hóa việc lưu trữ thông tin khách hàng, khởi tạo khoản vay với hai mốc Deadline và đồng bộ danh sách tài sản thế chấp vào hệ thống.
* Event 2 : Tính toán công nợ thời gian thực
```
  CREATE OR ALTER FUNCTION fn_CalcMoneyContract
(
    @ContractID INT,
    @TargetDate DATE
)
RETURNS TABLE
AS
RETURN
(
    WITH Base AS
    (
        SELECT
            h.MaHD,
            h.TienGoc,
            h.LaiSuatNgay,
            h.NgayVay,
            h.Deadline1,
            h.Deadline2,
            h.TongDaTra,

            ------------------------------------------------
            -- Số ngày lãi đơn
            ------------------------------------------------

            CASE
                WHEN @TargetDate <= h.Deadline1
                THEN DATEDIFF(
                        DAY,
                        h.NgayVay,
                        @TargetDate
                     )

                ELSE DATEDIFF(
                        DAY,
                        h.NgayVay,
                        h.Deadline1
                     )
            END AS SoNgayLaiDon,

            ------------------------------------------------
            -- Số ngày lãi kép
            ------------------------------------------------

            CASE
                WHEN @TargetDate <= h.Deadline1
                THEN 0

                ELSE DATEDIFF(
                        DAY,
                        h.Deadline1,
                        @TargetDate
                     )
            END AS SoNgayLaiKep

        FROM HopDong h
        WHERE h.MaHD = @ContractID
    )

    SELECT

        b.MaHD,

        b.TienGoc,

        ------------------------------------------------
        -- Lãi đơn
        ------------------------------------------------

        ROUND(
            b.TienGoc
            * b.LaiSuatNgay
            * b.SoNgayLaiDon,
            2
        ) AS TienLaiDon,

        ------------------------------------------------
        -- Lãi kép
        ------------------------------------------------

        ROUND(
            CASE
                WHEN b.SoNgayLaiKep > 0
                THEN
                    (
                        b.TienGoc
                        +
                        (
                            b.TienGoc
                            * b.LaiSuatNgay
                            * b.SoNgayLaiDon
                        )
                    )
                    *
                    (
                        POWER(
                            1 + b.LaiSuatNgay,
                            b.SoNgayLaiKep
                        ) - 1
                    )
                ELSE 0
            END,
            2
        ) AS TienLaiKep,

        ------------------------------------------------
        -- Tổng phải trả
        ------------------------------------------------

        ROUND(
            b.TienGoc
            +
            (
                b.TienGoc
                * b.LaiSuatNgay
                * b.SoNgayLaiDon
            )
            +
            (
                CASE
                    WHEN b.SoNgayLaiKep > 0
                    THEN
                        (
                            b.TienGoc
                            +
                            (
                                b.TienGoc
                                * b.LaiSuatNgay
                                * b.SoNgayLaiDon
                            )
                        )
                        *
                        (
                            POWER(
                                1 + b.LaiSuatNgay,
                                b.SoNgayLaiKep
                            ) - 1
                        )
                    ELSE 0
                END
            ),
            2
        ) AS TongTienPhaiTra

    FROM Base b
);
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/8a1e7cda-cecd-490e-b3fa-4bfd65c48594" />
Em đã cài đặt Function tính toán công nợ sử dụng hàm lũy thừa POWER để xử lý chính xác cơ chế chuyển đổi từ lãi đơn sang lãi kép ngay khi hợp đồng vượt quá mốc
*Event 3: Xử lý trả nợ và hoàn trả tài sản
```
CREATE OR ALTER PROCEDURE sp_XuLyTraNo
(
    @MaHD INT,
    @SoTienTra DECIMAL(18,2),
    @GhiChu NVARCHAR(500) = NULL
)
AS
BEGIN

    SET NOCOUNT ON;

    BEGIN TRANSACTION;

    BEGIN TRY

        ------------------------------------------------
        -- Kiểm tra hợp đồng tồn tại
        ------------------------------------------------

        IF NOT EXISTS
        (
            SELECT 1
            FROM HopDong
            WHERE MaHD = @MaHD
        )
        BEGIN
            THROW 50030,
            N'Hợp đồng không tồn tại.',
            1;
        END

        ------------------------------------------------
        -- Kiểm tra đã thanh lý chưa
        ------------------------------------------------

        IF EXISTS
        (
            SELECT 1
            FROM HopDong
            WHERE MaHD = @MaHD
            AND TrangThai = N'ThanhLy'
        )
        BEGIN
            THROW 50031,
            N'Tài sản đã bị thanh lý. Không thể thu tiền hoặc trả đồ.',
            1;
        END

        ------------------------------------------------
        -- Lấy tổng nợ hiện tại
        ------------------------------------------------

        DECLARE @TongNo DECIMAL(18,2);
        DECLARE @ConLai DECIMAL(18,2);

        SELECT
            @TongNo = TongNo,
            @ConLai = ConLai
        FROM fn_TinhLai
        (
            @MaHD,
            CAST(GETDATE() AS DATE)
        );

        ------------------------------------------------
        -- Kiểm tra số tiền trả
        ------------------------------------------------

        IF @SoTienTra <= 0
        BEGIN
            THROW 50032,
            N'Số tiền trả phải lớn hơn 0.',
            1;
        END

        IF @SoTienTra > @ConLai
        BEGIN
            THROW 50033,
            N'Số tiền trả vượt quá dư nợ.',
            1;
        END

        ------------------------------------------------
        -- Ghi nhận thanh toán
        ------------------------------------------------

        INSERT INTO ThanhToan
        (
            MaHD,
            NgayThanhToan,
            SoTien,
            GhiChu
        )
        VALUES
        (
            @MaHD,
            GETDATE(),
            @SoTienTra,
            @GhiChu
        );

        ------------------------------------------------
        -- Cập nhật tổng đã trả
        ------------------------------------------------

        UPDATE HopDong
        SET TongDaTra =
            TongDaTra + @SoTienTra
        WHERE MaHD = @MaHD;

        ------------------------------------------------
        -- Tính lại dư nợ
        ------------------------------------------------

        SELECT
            @ConLai = ConLai
        FROM fn_TinhLai
        (
            @MaHD,
            CAST(GETDATE() AS DATE)
        );

        ------------------------------------------------
        -- TRẢ HẾT NỢ
        ------------------------------------------------

        IF @ConLai <= 0
        BEGIN

            --------------------------------------------
            -- Đóng hợp đồng
            --------------------------------------------

            UPDATE HopDong
            SET
                TrangThai = N'DongHD',
                NgayDongHD = GETDATE()
            WHERE MaHD = @MaHD;

            --------------------------------------------
            -- Trả tài sản cho khách
            --------------------------------------------

            UPDATE ts
            SET TrangThai = N'DaTra'
            FROM TaiSan ts
            INNER JOIN HopDong_TaiSan hts
                ON ts.MaTS = hts.MaTS
            WHERE hts.MaHD = @MaHD;

            --------------------------------------------
            -- Log
            --------------------------------------------

            INSERT INTO LichSuLog
            (
                MaHD,
                HanhDong,
                NoiDung
            )
            VALUES
            (
                @MaHD,
                N'ThanToanDayDu',
                N'Khách đã thanh toán toàn bộ công nợ.'
            );

        END

        ------------------------------------------------
        -- CHƯA TRẢ HẾT
        ------------------------------------------------

        ELSE
        BEGIN

            UPDATE HopDong
            SET TrangThai = N'DangTraGop'
            WHERE MaHD = @MaHD;

            INSERT INTO LichSuLog
            (
                MaHD,
                HanhDong,
                NoiDung
            )
            VALUES
            (
                @MaHD,
                N'ThanhToanMotPhan',

                N'Số tiền đã trả: '
                + CAST(@SoTienTra AS NVARCHAR(50))

                + N' | Dư nợ còn lại: '
                + CAST(@ConLai AS NVARCHAR(50))
            );

        END

        COMMIT;

    END TRY

    BEGIN CATCH

        ROLLBACK;
        THROW;

    END CATCH

END;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/b2ff0b40-f28e-4170-adee-349167e6fd59" />
Store Procedure này cho phép xử lý trả nợ từng phần, cập nhật trạng thái hợp đồng và tự động gợi ý danh sách tài sản có thể hoàn trả dựa trên điều kiện giá trị tài sản còn lại bao phủ được dư nợ.

*Event 4 : Truy vấn danh sách nợ xấu (Nợ khó đòi)
```
CREATE OR ALTER FUNCTION fn_DuNoTuongLai
(
    @MaHD INT,
    @NgayTinh DATE
)
RETURNS DECIMAL(18,2)
AS
BEGIN

    DECLARE @TongNo DECIMAL(18,2);

    SELECT
        @TongNo = TongNo
    FROM fn_TinhLai
    (
        @MaHD,
        @NgayTinh
    );

    RETURN ISNULL(@TongNo, 0);

END;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/0d5767fc-6a6a-487a-8e46-14543a3379c6" />

*Event 5 : Quản lý thanh lý tài sản (Trigger)
```
/* ============================================================
   EVENT 5: QUẢN LÝ THANH LÝ TÀI SẢN
   VERSION FULL
============================================================ */


/* ============================================================
   1. NÂNG CẤP TRẠNG THÁI TÀI SẢN
============================================================ */

ALTER TABLE TaiSan
DROP CONSTRAINT CHK_TaiSan_TrangThai;
GO

ALTER TABLE TaiSan
ADD CONSTRAINT CHK_TaiSan_TrangThai
CHECK
(
    TrangThai IN
    (
        N'SanSang',
        N'DangCam',
        N'DaTra',
        N'SanSangThanhLy',
        N'DaBanThanhLy'
    )
);
GO


/* ============================================================
   2. NÂNG CẤP TRẠNG THÁI HỢP ĐỒNG
============================================================ */

ALTER TABLE HopDong
DROP CONSTRAINT CHK_HopDong_TrangThai;
GO

ALTER TABLE HopDong
ADD CONSTRAINT CHK_HopDong_TrangThai
CHECK
(
    TrangThai IN
    (
        N'DangVay',
        N'DangTraGop',
        N'QuaHan',
        N'DongHD',
        N'ThanhLy'
    )
);
GO


/* ============================================================
   3. TRIGGER:
   TỰ ĐỘNG CHUYỂN HỢP ĐỒNG -> QUÁ HẠN
============================================================ */

CREATE OR ALTER TRIGGER trg_HopDong_QuaHan
ON HopDong
AFTER INSERT, UPDATE
AS
BEGIN

    SET NOCOUNT ON;

    UPDATE h
    SET TrangThai = N'QuaHan'

    FROM HopDong h

    INNER JOIN inserted i
        ON h.MaHD = i.MaHD

    WHERE
        h.TrangThai = N'DangVay'
        AND h.Deadline1 < CAST(GETDATE() AS DATE);

END;
GO


/* ============================================================
   4. TRIGGER:
   CHUYỂN TÀI SẢN -> SẴN SÀNG THANH LÝ
============================================================ */

CREATE OR ALTER TRIGGER trg_TaiSan_SanSangThanhLy
ON HopDong
AFTER UPDATE
AS
BEGIN

    SET NOCOUNT ON;

    UPDATE ts
    SET TrangThai = N'SanSangThanhLy'

    FROM TaiSan ts

    INNER JOIN HopDong_TaiSan hts
        ON ts.MaTS = hts.MaTS

    INNER JOIN inserted i
        ON hts.MaHD = i.MaHD

    WHERE
        i.TrangThai = N'QuaHan'
        AND i.Deadline2 < CAST(GETDATE() AS DATE);

END;
GO


/* ============================================================
   5. TRIGGER:
   CHUYỂN TÀI SẢN -> ĐÃ BÁN THANH LÝ
============================================================ */

CREATE OR ALTER TRIGGER trg_TaiSan_DaBanThanhLy
ON HopDong
AFTER UPDATE
AS
BEGIN

    SET NOCOUNT ON;

    UPDATE ts
    SET TrangThai = N'DaBanThanhLy'

    FROM TaiSan ts

    INNER JOIN HopDong_TaiSan hts
        ON ts.MaTS = hts.MaTS

    INNER JOIN inserted i
        ON hts.MaHD = i.MaHD

    WHERE
        i.TrangThai = N'ThanhLy';

END;
GO


/* ============================================================
   6. TRIGGER:
   LOG THAY ĐỔI TRẠNG THÁI HỢP ĐỒNG
============================================================ */

CREATE OR ALTER TRIGGER trg_Log_TrangThaiHopDong
ON HopDong
AFTER UPDATE
AS
BEGIN

    SET NOCOUNT ON;

    INSERT INTO LichSuLog
    (
        MaHD,
        HanhDong,
        NoiDung
    )

    SELECT

        i.MaHD,

        N'CapNhatTrangThai',

        N'Trạng thái chuyển từ ['
        + ISNULL(d.TrangThai, N'NULL')
        + N'] sang ['
        + ISNULL(i.TrangThai, N'NULL')
        + N']'

    FROM inserted i

    INNER JOIN deleted d
        ON i.MaHD = d.MaHD

    WHERE
        i.TrangThai <> d.TrangThai;

END;
GO


/* ============================================================
   7. PROCEDURE CẬP NHẬT TRẠNG THÁI HÀNG NGÀY
============================================================ */

CREATE OR ALTER PROCEDURE sp_CapNhatTrangThaiHangNgay
AS
BEGIN

    SET NOCOUNT ON;

    DECLARE @Today DATE =
        CAST(GETDATE() AS DATE);

    ------------------------------------------------
    -- Chuyển sang QUÁ HẠN
    ------------------------------------------------

    UPDATE HopDong
    SET TrangThai = N'QuaHan'

    WHERE
        TrangThai IN
        (
            N'DangVay',
            N'DangTraGop'
        )

        AND Deadline1 < @Today;

    ------------------------------------------------
    -- Chuyển tài sản sang
    -- SẴN SÀNG THANH LÝ
    ------------------------------------------------

    UPDATE ts
    SET TrangThai = N'SanSangThanhLy'

    FROM TaiSan ts

    INNER JOIN HopDong_TaiSan hts
        ON ts.MaTS = hts.MaTS

    INNER JOIN HopDong h
        ON h.MaHD = hts.MaHD

    WHERE
        h.TrangThai = N'QuaHan'
        AND h.Deadline2 < @Today;

END;
GO


/* ============================================================
   8. PROCEDURE THANH LÝ TÀI SẢN
============================================================ */

CREATE OR ALTER PROCEDURE sp_ThanhLyTaiSan
(
    @MaHD INT
)
AS
BEGIN

    SET NOCOUNT ON;

    BEGIN TRANSACTION;

    BEGIN TRY

        ------------------------------------------------
        -- Kiểm tra hợp đồng tồn tại
        ------------------------------------------------

        IF NOT EXISTS
        (
            SELECT 1
            FROM HopDong
            WHERE MaHD = @MaHD
        )
        BEGIN
            THROW 50050,
            N'Hợp đồng không tồn tại.',
            1;
        END

        ------------------------------------------------
        -- Kiểm tra đủ điều kiện thanh lý
        ------------------------------------------------

        IF NOT EXISTS
        (
            SELECT 1
            FROM HopDong
            WHERE
                MaHD = @MaHD
                AND TrangThai = N'QuaHan'
                AND Deadline2 < CAST(GETDATE() AS DATE)
        )
        BEGIN
            THROW 50051,
            N'Hợp đồng chưa đủ điều kiện thanh lý.',
            1;
        END

        ------------------------------------------------
        -- Cập nhật hợp đồng
        ------------------------------------------------

        UPDATE HopDong
        SET
            TrangThai = N'ThanhLy',
            NgayDongHD = GETDATE()
        WHERE MaHD = @MaHD;

        ------------------------------------------------
        -- Cập nhật tài sản
        ------------------------------------------------

        UPDATE ts
        SET TrangThai = N'DaBanThanhLy'

        FROM TaiSan ts

        INNER JOIN HopDong_TaiSan hts
            ON ts.MaTS = hts.MaTS

        WHERE hts.MaHD = @MaHD;

        ------------------------------------------------
        -- Ghi log
        ------------------------------------------------

        INSERT INTO LichSuLog
        (
            MaHD,
            HanhDong,
            NoiDung
        )
        VALUES
        (
            @MaHD,
            N'ThanhLyTaiSan',
            N'Tài sản đã được bán thanh lý.'
        );

        COMMIT;

    END TRY

    BEGIN CATCH

        ROLLBACK;
        THROW;

    END CATCH

END;
GO


/* ============================================================
   9. VIEW THEO DÕI TÀI SẢN THANH LÝ
============================================================ */

CREATE OR ALTER VIEW vw_TaiSanThanhLy
AS

SELECT

    h.MaHD,
    kh.HoTen,
    kh.SDT,

    ts.MaTS,
    ts.TenTaiSan,
    ts.GiaTriDinhGia,
    ts.TrangThai,

    h.TrangThai AS TrangThaiHopDong,

    h.Deadline1,
    h.Deadline2

FROM TaiSan ts

INNER JOIN HopDong_TaiSan hts
    ON ts.MaTS = hts.MaTS

INNER JOIN HopDong h
    ON h.MaHD = hts.MaHD

INNER JOIN KhachHang kh
    ON kh.MaKH = h.MaKH

WHERE ts.TrangThai IN
(
    N'SanSangThanhLy',
    N'DaBanThanhLy'
);
GO


/* ============================================================
   10. TEST DỮ LIỆU
============================================================ */

-- Xem trạng thái hợp đồng

SELECT
    MaHD,
    TrangThai,
    Deadline1,
    Deadline2
FROM HopDong;
GO


-- Xem trạng thái tài sản

SELECT
    MaTS,
    TenTaiSan,
    TrangThai
FROM TaiSan;
GO


-- Xem log hệ thống

SELECT *
FROM LichSuLog
ORDER BY ThoiGian DESC;
GO


-- Xem tài sản thanh lý

SELECT *
FROM vw_TaiSanThanhLy;
GO


PRINT N'===== EVENT 5 ĐÃ TẠO THÀNH CÔNG =====';
GO
```
<img width="1919" height="1042" alt="image" src="https://github.com/user-attachments/assets/e60626fb-8aa2-4fe3-8bf5-012dbca9c00c" />
Event 5 giúp hệ thống tự động quản lý nợ xấu và thanh lý tài sản bằng Trigger và Procedure. Khi hợp đồng quá Deadline1, hệ thống sẽ tự động chuyển sang trạng thái “Quá hạn”. Nếu tiếp tục vượt Deadline2, tài sản sẽ được chuyển sang “Sẵn sàng thanh lý”. Khi thực hiện thanh lý, trạng thái tài sản sẽ đổi thành “Đã bán thanh lý”. Ngoài ra hệ thống còn tự động ghi log lịch sử thay đổi trạng thái để dễ theo dõi và quản lý. Chức năng này giúp việc quản lý hợp đồng và tài sản trở nên tự động, chính xác và giống quy trình thực tế của cửa hàng cầm đồ.
# Phần 4 : Các sự kiện bổ sung

*Sự kiện gia hạn hợp đồng

Chức năng này cho phép khách hàng trả toàn bộ tiền lãi tính đến thời điểm hiện tại để được gia hạn hợp đồng và tránh bị tính lãi kép. Sau khi khách thanh toán tiền lãi, hệ thống sẽ cập nhật lại Deadline1 và Deadline2 sang mốc thời gian mới, đồng thời ghi nhận lịch sử gia hạn vào bảng log để dễ theo dõi. Điều này giúp khách có thêm thời gian trả nợ và hỗ trợ cửa hàng quản lý hợp đồng linh hoạt hơn.



