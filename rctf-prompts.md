# Kết quả:
https://chatgpt.com/c/6adb19b1-c590-83ec-872c-b8661bfaa1c8

# PROMPT 1:
## Prompt:
Role: Bạn là Senior Node.js Developer.
Context: Web bán nông sản sạch. Node 20, Express 4, MySQL 8,
  Sequelize. Dùng const/let, async/await, xóa mềm (deletedAt),
  phân trang ?page=1&limit=20.
Task: Tạo API Create Product (name, price, unit, stock).
  - Validate: price > 0, stock >= 0
  - Tách controller / service / route
Format: 3 file riêng, mỗi file 1 code block có tên file ở dòng đầu.

## Kết quả:
// services/product.service.js
const { Product } = require('../models');

const createProduct = async (data) => {
    const product = await Product.create({
        name: data.name,
        price: data.price,
        unit: data.unit,
        stock: data.stock,
    });

    return product;
};

module.exports = {
    createProduct,
};

// controllers/product.controller.js
const productService = require('../services/product.service');

const createProduct = async (req, res) => {
    try {
        const { name, price, unit, stock } = req.body;

        // Validate dữ liệu bắt buộc
        if (
            typeof name !== 'string' ||
            !name.trim() ||
            typeof unit !== 'string' ||
            !unit.trim()
        ) {
            return res.status(400).json({
                message: 'Name và unit là bắt buộc.',
            });
        }

        // Validate price > 0
        if (
            price === undefined ||
            price === null ||
            price === '' ||
            !Number.isFinite(Number(price)) ||
            Number(price) <= 0
        ) {
            return res.status(400).json({
                message: 'Price phải là số lớn hơn 0.',
            });
        }

        // Validate stock >= 0
        if (
            stock === undefined ||
            stock === null ||
            stock === '' ||
            !Number.isFinite(Number(stock)) ||
            Number(stock) < 0
        ) {
            return res.status(400).json({
                message: 'Stock phải là số lớn hơn hoặc bằng 0.',
            });
        }

        const product = await productService.createProduct({
            name: name.trim(),
            price: Number(price),
            unit: unit.trim(),
            stock: Number(stock),
        });

        return res.status(201).json({
            message: 'Tạo sản phẩm thành công.',
            data: product,
        });
    } catch (error) {
        console.error('Create product error:', error);

        return res.status(500).json({
            message: 'Lỗi server.',
        });
    }
};

module.exports = {
    createProduct,
};

// routes/product.route.js
const express = require('express');
const router = express.Router();

const productController = require('../controllers/product.controller');

router.post('/', productController.createProduct);

module.exports = router;

Cách đăng ký route
Trong file app.js, thêm đoạn sau:

const express = require('express');
const app = express();

app.use(express.json());

const productRoute = require('./routes/product.route');

app.use('/api/products', productRoute);

module.exports = app;

## AI sai: đưa ra 4 file(gồm cả app.js) thay vì chỉ 3 file trong prompt.

# PROMPT 2:
## Prompt:
Role: Bạn là Senior Python Developer.
Context: Viết chương trình Python tính lượng phân bón NPK cần sử dụng theo diện tích đất. Sử dụng Python 3, dùng biến và hàm theo quy ước PEP 8. Có xử lý dữ liệu đầu vào và thông báo lỗi rõ ràng.
Task: Viết hàm Python tính lượng phân NPK theo diện tích.
- Input: Diện tích đất (m²) và định mức phân NPK (kg/m²).
- Công thức: Lượng phân NPK = Diện tích * Định mức.
- Validate: Diện tích phải lớn hơn 0; nếu diện tích ≤ 0 thì báo lỗi.
- Validate: Định mức phân NPK phải lớn hơn 0; nếu không hợp lệ thì báo lỗi.
- Xử lý trường hợp đầu vào không phải số hoặc không thể chuyển thành số.
- Tách riêng hàm tính toán và phần nhập dữ liệu, hiển thị kết quả.
- Không sử dụng thư viện ngoài.
Format: Trình bày code trong 1 code block, ghi tên file calc_npk.py ở dòng đầu.

## Kết quả:
# calc_npk.py

def calculate_npk(area, rate):
    """Tính lượng phân NPK cần sử dụng theo diện tích đất."""
    if area <= 0:
        raise ValueError("Diện tích đất phải lớn hơn 0 m².")

    if rate <= 0:
        raise ValueError("Định mức phân NPK phải lớn hơn 0 kg/m².")

    return area * rate


def main():
    """Nhập dữ liệu, tính toán và hiển thị kết quả."""
    try:
        area = float(input("Nhập diện tích đất (m²): "))
        rate = float(input("Nhập định mức phân NPK (kg/m²): "))

        npk_amount = calculate_npk(area, rate)

        print(f"Lượng phân NPK cần sử dụng: {npk_amount:.2f} kg")

    except ValueError as error:
        print(f"Lỗi: {error}")


if __name__ == "__main__":
    main()

# PROMPT 3:
## Prompt:
Role: Bạn là Senior SQL Developer.
Context: Hệ thống quản lý sản xuất nông nghiệp, sử dụng MySQL 8. Cơ sở dữ liệu lưu thông tin các vụ mùa, giống lúa và sản lượng thu hoạch. Sử dụng SQL chuẩn của MySQL, đặt tên cột dễ hiểu và xử lý dữ liệu thiếu hợp lý.
Task: Viết câu SQL tính sản lượng trung bình theo vụ và giống lúa.
- Sử dụng hàm tổng hợp `AVG()` để tính sản lượng trung bình.
- Nhóm dữ liệu theo vụ mùa và giống lúa bằng `GROUP BY`.
- Sử dụng `JOIN` nếu thông tin vụ mùa và giống lúa nằm ở các bảng khác nhau.
- Chỉ tính trung bình từ các bản ghi có sản lượng hợp lệ, không phải `NULL`.
- Hiển thị tên vụ mùa, tên giống lúa và sản lượng trung bình.
- Đặt tên cho cột sản lượng trung bình là `average_yield`.
- Sắp xếp kết quả theo vụ mùa và giống lúa.
- Giả sử có 3 bảng:
  - `harvests`: `id`, `season_id`, `rice_variety_id`, `yield`
  - `seasons`: `id`, `season_name`
  - `rice_varieties`: `id`, `variety_name`
- Không sử dụng dữ liệu giả định ngoài cấu trúc bảng đã cho.
Format:Trình bày câu SQL trong 1 code block, ghi tên file `avg_yield.sql` ở dòng đầu.

## Kết quả:
-- avg_yield.sql

SELECT
    s.season_name,
    rv.variety_name,
    AVG(h.`yield`) AS average_yield
FROM harvests AS h
INNER JOIN seasons AS s
    ON h.season_id = s.id
INNER JOIN rice_varieties AS rv
    ON h.rice_variety_id = rv.id
WHERE h.`yield` IS NOT NULL
GROUP BY
    s.id,
    s.season_name,
    rv.id,
    rv.variety_name
ORDER BY
    s.season_name ASC,
    rv.variety_name ASC;