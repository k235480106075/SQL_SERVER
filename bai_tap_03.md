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


