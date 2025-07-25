<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title id="page-title">Shop Chính Hãng</title>
    <!-- Tải Tailwind CSS từ CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Tải Font Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Tải Font Awesome cho icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #fdf8f4; /* Màu nền ấm dịu */
            color: #333;
            transition: background-image 0.5s ease-in-out, background-color 0.5s ease-in-out;
            background-size: cover;
            background-position: center;
            background-attachment: fixed; /* Giữ hình nền cố định khi cuộn */
            display: flex;
            flex-direction: column;
            min-height: 10vh; /* Changed from min-height: 100vh for better responsiveness */
        }
        .modal-overlay {
            background-color: rgba(0, 0, 0, 0.6);
            backdrop-filter: blur(5px);
            transition: opacity 0.3s ease-in-out;
            opacity: 0;
            pointer-events: none;
        }
        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }
        .modal-content {
            transform: scale(0.9);
            transition: transform 0.3s ease-in-out;
        }
        .modal-overlay.active .modal-content {
            transform: scale(1);
        }

        .price-original {
            text-decoration: line-through;
            color: #999;
        }
        .price-discounted {
            color: #e53e3e; /* Red color for discounted price */
            font-weight: 600;
        }
        .order-status-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: relative;
            margin-bottom: 2rem;
        }
        .order-status-step {
            flex: 1;
            text-align: center;
            padding: 0.4rem 0;
            border-bottom: 3px solid #ccc;
            color: #999;
            font-weight: 500;
            position: relative;
            transition: all 0.3s ease-in-out;
        }
        .order-status-step.active {
            border-color: #4CAF50; /* Green */
            color: #4CAF50;
        }
        .order-status-step::after {
            content: '';
            position: absolute;
            bottom: -3px;
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 0;
            border-left: 8px solid transparent;
            border-right: 8px solid transparent;
            border-bottom: 8px solid transparent;
            transition: all 0.3s ease-in-out;
        }
        .order-status-step.active::after {
            border-bottom-color: #4CAF50;
        }
        .tab-button.active {
            background-color: #4CAF50;
            color: white;
        }

        /* Styles for selected option with checkmark */
        .option-button.selected {
            background-color: #d1e7dd; /* Light green background */
            border-color: #28a745; /* Darker green border */
            color: #212529;
            position: relative;
        }
        .option-button.selected::after {
            content: "\f00c"; /* Font Awesome checkmark icon */
            font-family: "Font Awesome 5 Free";
            font-weight: 900;
            position: absolute;
            top: 5px;
            right: 5px;
            color: #28a745;
            font-size: 0.8em;
        }
        /* CSS cho trường địa chỉ bị làm mờ */
        .blurred-content {
            filter: blur(5px);
            transition: filter 0.2s ease-in-out;
        }
        .masked-input-wrapper { /* Để căn chỉnh nút toggle bên trong input */
            position: relative;
        }
        .masked-input-wrapper .toggle-visibility-btn {
            position: absolute;
            top: 50%; /* Căn giữa theo chiều dọc */
            transform: translateY(-50%); /* Dịch chuyển lên 50% chiều cao của chính nó */
            right: 0.5rem; /* Điều chỉnh để dịch chuyển icon vào trong 0.5rem (khoảng 8px) */
            padding: 0 0.75rem;
            display: flex; /* Đảm bảo căn giữa nội dung bên trong */
            align-items: center; /* Đảm bảo căn giữa nội dung bên trong */
            color: #6b7280; /* text-gray-500 */
            background: transparent; /* Ensure button background is transparent */
            border: none;
            cursor: pointer;
        }
        .masked-input-wrapper textarea + .toggle-visibility-btn { /* Specific for textarea */
            top: 0.5rem; /* Align button with top of textarea */
            height: auto; /* Allow height to adjust */
        }
        .out-of-stock {
            color: #ef4444; /* red-500 */
            font-weight: 600;
        }
        /* New style for expandable content */
        .order-expandable-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
        }
        .order-expandable-content.expanded {
            max-height: 500px; /* Adjust as needed */
            transition: max-height 0.5s ease-in;
        }
    </style>
</head>
<body class="bg-fdf8f4 text-333">

    <!-- Header: Fixed at top, vibrant gradient -->
    <header class="fixed top-0 left-0 right-0 z-40 bg-gradient-to-r from-blue-600 to-purple-600 text-white shadow-xl py-4 px-6 md:px-10 lg:px-16 w-full flex items-center justify-between">
        <div class="flex items-center">
            <h1 id="shop-name-display" class="text-4xl font-bold rounded-full py-2 px-6 transition-all duration-300 ease-in-out hover:scale-105 cursor-pointer"><i class="fas fa-map-marker-alt mr-3"></i>Cửa hàng Nam Đẹp Trai</h1>
            <!-- Management Button -->
            <button id="open-shop-analytics-modal-btn" class="ml-6 bg-white bg-opacity-20 hover:bg-opacity-30 text-white font-medium py-2 px-4 rounded-full transition-all duration-200 shadow-md">
                <i class="fas fa-chart-line mr-2"></i>Quản Lý & Thống Kê
            </button>
        </div>
        <!-- Shop Address Display -->
        <div id="shop-address-container" class="flex flex-col items-end text-sm text-white text-right">
            <span class="font-semibold mb-1">Địa chỉ cửa hàng:</span>
            <span id="shop-address-display" class="text-xs italic text-gray-200">Chưa cập nhật</span>
            <button id="open-address-in-map-btn" class="mt-1 bg-white bg-opacity-20 hover:bg-opacity-30 text-white text-xs py-1 px-2 rounded-full transition-all duration-200">
                <i class="fas fa-map-marker-alt mr-1"></i>Xem trên bản đồ
            </button>
        </div>
    </header>

    <!-- Main content area (sidebar + sections), pushed down by header height -->
    <div class="flex flex-1 w-full mt-24"> <!-- mt-24 is approx header height. Adjust if header content changes -->
        <!-- Sidebar: Fixed, scrollable, takes initial height from body, smaller -->
        <aside class="fixed top-0 left-0 z-30 w-56 lg:w-64 h-full pt-24 bg-white p-4 rounded-xl shadow-lg overflow-y-auto border-r border-gray-200">
            <h2 class="text-xl font-semibold mb-6 text-gray-800">MENU</h2>
            <nav>
                <ul class="space-y-3"> <!-- Smaller spacing for buttons -->
                    <li><button id="show-products-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100 active"><i class="fas fa-boxes mr-2"></i>Gian Hàng</button></li>
                    <li><button id="show-created-orders-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100"><i class="fas fa-clipboard-list mr-2"></i>Đơn Hàng Đã Tạo</button></li>
                    <li><button id="show-shipping-orders-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100"><i class="fas fa-truck mr-2"></i>Đơn Hàng Đang Vận Chuyển</button></li>
                    <li><button id="show-delivered-orders-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100"><i class="fas fa-check-circle mr-2"></i>Đơn Hàng Đã Giao</button></li>
                    <li><button id="open-management-modal-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100"><i class="fas fa-cogs mr-2"></i>Quản Lý Cửa Hàng</button></li>
                    <li><button id="open-settings-modal-btn" class="tab-button w-full text-left p-3 rounded-lg font-medium text-base transition-all duration-200 hover:bg-gray-100"><i class="fas fa-cog mr-2"></i>Cài Đặt Cửa Hàng</button></li>
                </ul>
            </nav>
        </aside>

        <!-- Main content sections: fills remaining width, scrolls its own content -->
        <section class="flex-1 ml-56 lg:ml-64 p-6 rounded-xl shadow-lg bg-white overflow-y-auto"> <!-- Adjusted margin-left -->
            <!-- Product List Section -->
            <div id="product-list-section" class="active-section">
                <h2 class="text-3xl font-semibold mb-6 text-gray-800">Sản Phẩm Nổi Bật</h2>
                <div id="product-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <!-- Products will be loaded here by JavaScript -->
                </div>
            </div>

            <!-- Created Orders Section -->
            <div id="created-orders-section" class="hidden">
                <h2 class="text-3xl font-semibold mb-6 text-gray-800">Đơn Hàng Đã Tạo</h2>
                <div class="mb-4 flex items-center gap-2">
                    <input type="text" id="search-created-orders" placeholder="Tìm kiếm đơn hàng đã tạo..." class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <button id="clear-search-created-orders" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-times"></i></button>
                </div>
                <div id="created-orders-list" class="space-y-4">
                    <!-- Created orders will be loaded here -->
                </div>
            </div>

            <!-- Shipping Orders Section -->
            <div id="shipping-orders-section" class="hidden">
                <h2 class="text-3xl font-semibold mb-6 text-gray-800">Đơn Hàng Đang Vận Chuyển</h2>
                 <div class="mb-4 flex items-center gap-2">
                    <input type="text" id="search-shipping-orders" placeholder="Tìm kiếm đơn hàng đang vận chuyển..." class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <button id="clear-search-shipping-orders" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-times"></i></button>
                </div>
                <div id="shipping-orders-list" class="space-y-4">
                    <!-- Shipping orders will be loaded here -->
                </div>
            </div>

            <!-- Delivered Orders Section -->
            <div id="delivered-orders-section" class="hidden">
                <h2 class="text-3xl font-semibold mb-6 text-gray-800">Đơn Hàng Đã Giao</h2>
                <div class="mb-4 flex items-center gap-2">
                    <input type="text" id="search-delivered-orders" placeholder="Tìm kiếm đơn hàng đã giao..." class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <button id="clear-search-delivered-orders" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-times"></i></button>
                </div>
                <div id="delivered-orders-list" class="space-y-4">
                    <!-- Delivered orders will be loaded here -->
                </div>
            </div>
        </section>
    </div>

    <!-- Product Detail Modal -->
    <div id="product-detail-modal" class="hidden fixed inset-0 flex items-center justify-center z-50 modal-overlay p-4">
        <div class="bg-blue-50 rounded-3xl shadow-2xl p-8 max-w-4xl w-full flex flex-col md:flex-row gap-8 relative modal-content max-h-[85vh] overflow-y-auto">
            <button id="close-product-modal" class="absolute top-4 right-4 text-gray-600 hover:text-gray-800 text-3xl font-bold">&times;</button>
            <div class="md:w-1/2 flex items-center justify-center p-4">
                <img id="modal-product-image" src="" alt="Sản phẩm" class="max-h-96 w-auto object-contain mb-4 rounded-lg shadow-md">
            </div>
            <div class="md:w-1/2">
                <h3 id="modal-product-name" class="text-3xl font-bold mb-4 text-gray-900"></h3>
                <p class="text-xl font-semibold mb-2 text-gray-700">Giá cơ bản: <span id="modal-product-base-price" class="font-bold"></span></p>
                <p class="text-xl font-semibold mb-2 text-gray-700">Tổng giá: <span id="modal-product-price-display" class="price-original"></span></p>
                <p id="modal-product-discount-display" class="text-xl font-semibold mb-4 price-discounted hidden"></p>
                <p id="modal-shipping-display" class="text-green-600 font-semibold mb-4 hidden">Phí vận chuyển: Miễn Phí</p> <!-- New element for freeship -->

                <div id="product-options" class="mb-6 space-y-4">
                    <!-- Options (color, storage) will be dynamically loaded here -->
                </div>
                
                <p class="text-gray-600 mb-2">Số lượng đã bán: <span id="modal-product-sold">N/A</span></p>
                <p class="text-gray-600 mb-4">Số lượng còn lại: <span id="modal-product-remaining">N/A</span></p>
                <p id="modal-product-description" class="text-gray-700 mb-4 text-sm"></p>
                

                <div class="mb-6">
                    <label for="voucher-code" class="block text-gray-700 text-sm font-bold mb-2">Mã Voucher:</label>
                    <div class="flex">
                        <input type="text" id="voucher-code" class="shadow appearance-none border rounded-l-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Nhập mã voucher">
                        <button id="apply-voucher-btn" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-r-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-all duration-200"><i class="fas fa-ticket-alt"></i> Áp Dụng</button>
                    </div>
                </div>

                <button id="create-order-button" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg"><i class="fas fa-plus-circle mr-2"></i>Tạo Đơn Hàng</button>
            </div>
        </div>
    </div>

    <!-- Order Creation Modal -->
    <div id="order-creation-modal" class="hidden fixed inset-0 flex items-center justify-center z-50 modal-overlay p-4">
        <div class="bg-blue-50 rounded-3xl shadow-2xl p-8 max-w-2xl w-full relative modal-content max-h-[85vh] overflow-y-auto">
            <button id="close-order-modal" class="absolute top-4 right-4 text-gray-600 hover:text-gray-800 text-3xl font-bold">&times;</button>
            <h3 class="text-3xl font-bold mb-6 text-gray-900 text-center">Tạo Đơn Hàng Mới</h3>

            <div class="mb-6">
                <p class="text-gray-700 text-lg mb-2">Mã đơn hàng: <span id="order-id-display" class="font-semibold text-blue-700"></span></p>
            </div>

            <form id="order-form" class="space-y-4">
                <div>
                    <label for="customer-name" class="block text-gray-700 text-lg font-bold mb-2">Họ tên:</label>
                    <input type="text" id="customer-name" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required>
                </div>
                <div>
                    <label for="customer-phone" class="block text-gray-700 text-lg font-bold mb-2">Số điện thoại:</label>
                    <div class="masked-input-wrapper">
                        <input type="tel" id="customer-phone" class="shadow appearance-none border rounded-lg w-full py-3 px-4 pr-12 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required>
                        <button type="button" class="toggle-visibility-btn" data-target-id="customer-phone">
                            <i class="fas fa-eye"></i>
                        </button>
                    </div>
                </div>
                <div>
                    <label for="customer-address" class="block text-gray-700 text-lg font-bold mb-2">Địa chỉ:</label>
                    <div class="masked-input-wrapper">
                        <textarea id="customer-address" class="shadow appearance-none border rounded-lg w-full py-3 px-4 pr-12 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 h-24 resize-none" required></textarea>
                        <button type="button" class="toggle-visibility-btn" data-target-id="customer-address">
                            <i class="fas fa-eye"></i>
                        </button>
                    </div>
                </div>

                <div class="order-status-bar">
                    <div class="order-status-step active" data-status="created">Tạo Đơn Hàng</div>
                    <div class="order-status-step" data-status="shipping">Đang Vận Chuyển</div>
                    <div class="order-status-step" data-status="delivered">Đã Giao Hàng</div>
                </div>

                <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg"><i class="fas fa-check-square mr-2"></i>Xác Nhận Đơn Hàng</button>
            </form>
        </div>
    </div>

    <!-- Shop Management Modal -->
    <div id="shop-management-modal" class="hidden fixed inset-0 flex items-center justify-center z-50 modal-overlay p-4">
        <div class="bg-blue-50 rounded-3xl shadow-2xl p-8 max-w-4xl w-full relative overflow-y-auto max-h-[90vh] modal-content">
            <button id="close-management-modal" class="absolute top-4 right-4 text-gray-600 hover:text-gray-800 text-3xl font-bold">&times;</button>
            <h3 class="text-3xl font-bold mb-6 text-gray-900 text-center">Quản Lý Cửa Hàng</h3>

            <!-- Manage Existing Products Section -->
            <div class="mb-8 border-b pb-6 border-gray-200">
                <h4 class="text-2xl font-semibold mb-4 text-gray-800">Quản lý Sản phẩm Hiện Có</h4>
                <div id="product-management-list" class="space-y-4">
                    <!-- Existing products will be loaded here with edit/delete buttons -->
                    <p class="text-gray-500 italic">Đang tải danh sách sản phẩm...</p>
                </div>
            </div>


            <!-- Add/Edit Product Section -->
            <div class="mb-8 border-b pb-6 border-gray-200">
                <h4 id="add-edit-product-title" class="text-2xl font-semibold mb-4 text-gray-800">Thêm Mặt Hàng Mới</h4>
                <form id="add-edit-product-form" class="space-y-4">
                    <input type="hidden" id="edit-product-id">
                    <div>
                        <label for="new-product-name" class="block text-gray-700 text-lg font-bold mb-2">Tên Sản Phẩm:</label>
                        <input type="text" id="new-product-name" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required>
                    </div>
                    <div>
                        <label for="new-product-base-price" class="block text-gray-700 text-lg font-bold mb-2">Giá Cơ Bản (VND):</label>
                        <input type="number" id="new-product-base-price" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required min="0">
                    </div>
                    
                    <div>
                        <label for="new-product-image" class="block text-gray-700 text-lg font-bold mb-2">URL Hình Ảnh Chính:</label>
                        <div class="flex gap-2">
                            <input type="url" id="new-product-image" class="shadow appearance-none border rounded-l-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="https://example.com/image.jpg">
                            <button type="button" id="upload-main-image-btn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-r-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-upload mr-2"></i>Tải ảnh lên</button>
                        </div>
                    </div>
                    <div>
                        <label for="new-product-description" class="block text-gray-700 text-lg font-bold mb-2">Mô tả sản phẩm:</label>
                        <textarea id="new-product-description" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 h-20 resize-none"></textarea>
                    </div>

                    <!-- Dynamic Color Options -->
                    <div class="border p-4 rounded-lg bg-gray-50">
                        <h5 class="text-xl font-semibold mb-3 text-gray-700">Tùy Chọn Màu Sắc (kèm Giá tác động)</h5>
                        <div id="color-options-container" class="space-y-3">
                            <!-- Color inputs will be added here -->
                        </div>
                        <button type="button" id="add-color-option-btn" class="mt-4 bg-purple-500 hover:bg-purple-600 text-white font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-plus mr-2"></i>Thêm Màu Sắc</button>
                    </div>

                    <!-- Dynamic Storage Options -->
                    <div class="border p-4 rounded-lg bg-gray-50">
                        <h5 class="text-xl font-semibold mb-3 text-gray-700">Tùy Chọn Dung Lượng (kèm Giá tác động)</h5>
                        <div id="storage-options-container" class="space-y-3">
                            <!-- Storage inputs will be added here -->
                        </div>
                        <button type="button" id="add-storage-option-btn" class="mt-4 bg-purple-500 hover:bg-purple-600 text-white font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-plus mr-2"></i>Thêm Dung Lượng</button>
                    </div>

                    <!-- Dynamic Product Variants (Combinations of Color/Storage with their own quantities and price impact) -->
                    <div class="border p-4 rounded-lg bg-yellow-50">
                        <h5 class="text-xl font-semibold mb-3 text-gray-700">Các Biến Thể Sản Phẩm (Màu sắc + Dung lượng, kèm số lượng & Giá tác động riêng)</h5>
                        <div id="variants-container" class="space-y-3">
                            <!-- Variants will be added here -->
                        </div>
                        <button type="button" id="add-variant-btn" class="mt-4 bg-orange-500 hover:bg-orange-600 text-white font-bold py-2 px-4 rounded-lg transition-all duration-200"><i class="fas fa-plus mr-2"></i>Thêm Biến Thể</button>
                        <p class="text-sm text-gray-600 mt-2">Lưu ý: Chỉ thêm biến thể sau khi đã định nghĩa các Tùy chọn Màu sắc và Dung lượng phía trên.</p>
                    </div>

                    <button type="submit" id="submit-product-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg mt-6"><i class="fas fa-save mr-2"></i>Thêm Sản Phẩm</button>
                    <button type="button" id="cancel-edit-btn" class="hidden bg-gray-500 hover:bg-gray-600 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg mt-2"><i class="fas fa-times-circle mr-2"></i>Hủy Chỉnh Sửa</button>
                </form>
            </div>

            <!-- Add New Voucher Section -->
            <div class="mt-8">
                <h4 class="text-2xl font-semibold mb-4 text-gray-800">Thêm Mã Voucher Mới</h4>
                <form id="add-voucher-form" class="space-y-4">
                    <div>
                        <label for="new-voucher-code" class="block text-gray-700 text-lg font-bold mb-2">Mã Voucher:</label>
                        <input type="text" id="new-voucher-code" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required>
                    </div>
                    <div>
                        <label for="new-voucher-value" class="block text-gray-700 text-lg font-bold mb-2">Giá Trị Voucher (ví dụ: 0.10 cho 10%, 50000 cho 50.000 VND):</label>
                        <input type="number" step="any" id="new-voucher-value" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required min="0">
                    </div>
                    <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg"><i class="fas fa-plus-square mr-2"></i>Thêm Voucher</button>
                </form>

                <div class="mt-8">
                    <h4 class="text-2xl font-semibold mb-4 text-gray-800">Voucher Hiện Có</h4>
                    <div id="current-vouchers-list" class="space-y-2">
                        <!-- Current vouchers will be displayed here -->
                        <p class="text-gray-500 italic">Chưa có voucher nào.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Shop Settings Modal -->
    <div id="shop-settings-modal" class="hidden fixed inset-0 flex items-center justify-center z-50 modal-overlay p-4">
        <div class="bg-blue-50 rounded-3xl shadow-2xl p-8 max-w-xl w-full relative modal-content overflow-y-auto">
            <button id="close-settings-modal" class="absolute top-4 right-4 text-gray-600 hover:text-gray-800 text-3xl font-bold">&times;</button>
            <h3 class="text-3xl font-bold mb-6 text-gray-900 text-center">Cài Đặt Cửa Hàng</h3>

            <form id="shop-settings-form" class="space-y-4">
                <div>
                    <label for="shop-name-input" class="block text-gray-700 text-lg font-bold mb-2">Tên Cửa Hàng:</label>
                    <input type="text" id="shop-name-input" class="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" required>
                </div>
                <div>
                    <label for="background-image-url" class="block text-gray-700 text-lg font-bold mb-2">URL Hình Nền:</label>
                    <div class="flex gap-2">
                        <input type="url" id="background-image-url" class="shadow appearance-none border rounded-l-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="https://example.com/background.jpg">
                        <button type="button" id="upload-background-btn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-r-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-upload mr-2"></i>Tải ảnh lên</button>
                    </div>
                    <small class="text-gray-500">Để trống nếu không muốn dùng hình nền.</small>
                </div>
                <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg w-full transition-all duration-200 shadow-lg"><i class="fas fa-save mr-2"></i>Lưu Cài Đặt</button>
            </form>
        </div>
    </div>

    <!-- Shop Analytics Modal -->
    <div id="shop-analytics-modal" class="hidden fixed inset-0 flex items-center justify-center z-50 modal-overlay p-4">
        <div class="bg-blue-50 rounded-3xl shadow-2xl p-8 max-w-3xl w-full relative modal-content max-h-[85vh] overflow-y-auto">
            <button id="close-analytics-modal" class="absolute top-4 right-4 text-gray-600 hover:text-gray-800 text-3xl font-bold">&times;</button>
            <h3 class="text-3xl font-bold mb-6 text-gray-900 text-center">Thống Kê Cửa Hàng</h3>

            <div class="mb-6 flex flex-col md:flex-row items-center gap-4">
                <div class="flex-1 w-full">
                    <label for="report-start-date" class="block text-gray-700 text-sm font-bold mb-2">Từ ngày:</label>
                    <input type="date" id="report-start-date" class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div class="flex-1 w-full">
                    <label for="report-end-date" class="block text-gray-700 text-sm font-bold mb-2">Đến ngày:</label>
                    <input type="date" id="report-end-date" class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <button id="view-analytics-report-btn" class="mt-4 md:mt-auto bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-5 rounded-lg transition-all duration-200 shadow-lg flex-shrink-0">
                    <i class="fas fa-chart-bar mr-2"></i>Xem Báo Cáo
                </button>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 text-center mt-8">
                <div class="bg-blue-50 p-6 rounded-lg shadow-md">
                    <h4 class="text-xl font-semibold text-blue-800 mb-2">Doanh Thu</h4>
                    <p id="revenue-display" class="text-3xl font-bold text-blue-600">0 VND</p>
                </div>
                <div class="bg-green-50 p-6 rounded-lg shadow-md">
                    <h4 class="text-xl font-semibold text-green-800 mb-2">Sản Phẩm Đã Bán</h4>
                    <p id="sales-quantity-display" class="text-3xl font-bold text-green-600">0</p>
                </div>
                <div class="bg-purple-50 p-6 rounded-lg shadow-md">
                    <h4 class="text-xl font-semibold text-purple-800 mb-2">Sản Phẩm Nhập Về</h4>
                    <p id="inbound-quantity-display" class="text-3xl font-bold text-purple-600">0</p>
                </div>
            </div>

            <div class="mt-8 pt-6 border-t border-gray-200">
                <h4 class="text-xl font-semibold mb-4 text-gray-800">Cập Nhật Địa Chỉ Cửa Hàng</h4>
                <label for="shop-address-settings-input" class="block text-gray-700 text-sm font-bold mb-2">Địa chỉ:</label>
                <div class="flex gap-2">
                    <input type="text" id="shop-address-settings-input" class="shadow appearance-none border rounded-l-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Nhập địa chỉ cửa hàng">
                    <button type="button" id="open-google-maps-btn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-r-lg transition-all duration-200 text-sm flex-shrink-0">
                        <i class="fas fa-map-marked-alt mr-2"></i>Chọn trên Map
                    </button>
                </div>
                <button id="save-shop-address-btn" class="mt-4 bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-5 rounded-lg w-full transition-all duration-200 shadow-lg">
                    <i class="fas fa-save mr-2"></i>Lưu Địa Chỉ
                </button>
            </div>
        </div>
    </div>


    <!-- Message Box -->
    <div id="message-box" class="hidden fixed bottom-6 right-6 bg-blue-600 text-white px-6 py-3 rounded-lg shadow-xl z-50 text-lg">
        Thông báo
    </div>

    <script>
        // Hàm hiển thị thông báo
        function showMessage(message, type = 'info') {
            const messageBox = document.getElementById('message-box');
            messageBox.textContent = message;
            messageBox.className = `hidden fixed bottom-6 right-6 px-6 py-3 rounded-lg shadow-xl z-50 text-lg`; // Reset classes
            if (type === 'success') {
                messageBox.classList.add('bg-green-600');
            } else if (type === 'error') {
                messageBox.classList.add('bg-red-600');
            } else {
                messageBox.classList.add('bg-blue-600');
            }
            messageBox.classList.remove('hidden');
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 3000); // Hide after 3 seconds
        }

        // Dữ liệu sản phẩm mẫu (đã được cập nhật với cấu trúc variants)
        let products = JSON.parse(localStorage.getItem('products')) || [
            {
                id: 'p1',
                name: 'iPhone 15 Pro Max',
                basePrice: 28000000,
                image: 'https://placehold.co/400x400/FF0000/FFFFFF?text=iPhone+15',
                description: 'iPhone 15 Pro Max là điện thoại thông minh cao cấp với chip A17 Bionic, camera Pro mạnh mẽ và màn hình Super Retina XDR.',
                options: {
                    color: [
                        { name: 'Titan Tự Nhiên', display_image: 'https://placehold.co/400x400/969696/FFFFFF?text=Titan+Tu+Nhien', price_impact: 0 },
                        { name: 'Titan Xanh', display_image: 'https://placehold.co/400x400/2A52BE/FFFFFF?text=Titan+Xanh', price_impact: 500000 },
                        { name: 'Titan Trắng', display_image: 'https://placehold.co/400x400/E0E0E0/000000?text=Titan+Trang', price_impact: 200000 }
                    ],
                    storage: [
                        { name: '128GB', price_impact: 0 },
                        { name: '256GB', price_impact: 1000000 },
                        { name: '512GB', price_impact: 2500000 },
                        { name: '1TB', price_impact: 4000000 }
                    ]
                },
                variants: [
                    { color: 'Titan Tự Nhiên', storage: '128GB', additional_price_impact: 0, sold: 10, remaining: 5 },
                    { color: 'Titan Tự Nhiên', storage: '256GB', additional_price_impact: 0, sold: 5, remaining: 15 },
                    { color: 'Titan Tự Nhiên', storage: '512GB', additional_price_impact: 0, sold: 2, remaining: 8 },
                    { color: 'Titan Tự Nhiên', storage: '1TB', additional_price_impact: 0, sold: 1, remaining: 4 },

                    { color: 'Titan Xanh', storage: '128GB', additional_price_impact: 0, sold: 8, remaining: 2 },
                    { color: 'Titan Xanh', storage: '256GB', additional_price_impact: 0, sold: 3, remaining: 7 },
                    { color: 'Titan Xanh', storage: '512GB', additional_price_impact: 0, sold: 0, remaining: 0 }, // Hết hàng
                    { color: 'Titan Xanh', storage: '1TB', additional_price_impact: 0, sold: 1, remaining: 9 },

                    { color: 'Titan Trắng', storage: '128GB', additional_price_impact: 0, sold: 7, remaining: 3 },
                    { color: 'Titan Trắng', storage: '256GB', additional_price_impact: 0, sold: 4, remaining: 6 },
                    { color: 'Titan Trắng', storage: '512GB', additional_price_impact: 0, sold: 2, remaining: 8 },
                    { color: 'Titan Trắng', storage: '1TB', additional_price_impact: 0, sold: 1, remaining: 9 },
                ]
            },
            {
                id: 'p2',
                name: 'Samsung Galaxy S24 Ultra',
                basePrice: 26000000,
                image: 'https://placehold.co/400x400/0000FF/FFFFFF?text=S24+Ultra'
                description: 'Samsung Galaxy S24 Ultra với S Pen tích hợp, camera 200MP và hiệu năng mạnh mẽ.',
                options: {
                    color: [
                        { name: 'Đen Phantom', display_image: 'https://placehold.co/400x400/333333/FFFFFF?text=Den+Phantom', price_impact: 0 },
                        { name: 'Xám', display_image: 'https://placehold.co/400x400/808080/FFFFFF?text=Xam', price_impact: 300000 }
                    ],
                    storage: [
                        { name: '256GB', price_impact: 0 },
                        { name: '512GB', price_impact: 1200000 },
                        { name: '1TB', price_impact: 2800000 }
                    ]
                },
                variants: [
                    { color: 'Đen Phantom', storage: '256GB', additional_price_impact: 0, sold: 12, remaining: 3 },
                    { color: 'Đen Phantom', storage: '512GB', additional_price_impact: 0, sold: 8, remaining: 7 },
                    { color: 'Đen Phantom', storage: '1TB', additional_price_impact: 0, sold: 5, remaining: 10 },

                    { color: 'Xám', storage: '256GB', additional_price_impact: 0, sold: 7, remaining: 8 },
                    { color: 'Xám', storage: '512GB', additional_price_impact: 0, sold: 4, remaining: 11 },
                    { color: 'Xám', storage: '1TB', additional_price_impact: 0, sold: 0, remaining: 0 }, // Hết hàng
                ]
            },
            {
                id: 'p3',
                name: 'MacBook Air M3',
                basePrice: 23000000,
                image: 'https://placehold.co/400x400/008000/FFFFFF?text=MacBook+Air',
                description: 'MacBook Air M3 siêu mỏng, nhẹ và mạnh mẽ với chip M3.',
                options: {
                    color: [
                        { name: 'Bạc', display_image: 'https://placehold.co/400x400/C0C0C0/000000?text=Bac', price_impact: 0 },
                        { name: 'Xám Không Gian', display_image: 'https://placehold.co/400x400/696969/FFFFFF?text=Xam+Khong+Gian', price_impact: 100000 }
                    ],
                    storage: [
                        { name: '256GB', price_impact: 0 },
                        { name: '512GB', price_impact: 1500000 },
                        { name: '1TB', price_impact: 3000000 }
                    ]
                },
                variants: [
                    { color: 'Bạc', storage: '256GB', additional_price_impact: 0, sold: 5, remaining: 10 },
                    { color: 'Bạc', storage: '512GB', additional_price_impact: 0, sold: 3, remaining: 7 },
                    { color: 'Bạc', storage: '1TB', additional_price_impact: 0, sold: 1, remaining: 4 },

                    { color: 'Xám Không Gian', storage: '256GB', additional_price_impact: 0, sold: 4, remaining: 6 },
                    { color: 'Xám Không Gian', storage: '512GB', additional_price_impact: 0, sold: 2, remaining: 8 },
                    { color: 'Xám Không Gian', storage: '1TB', additional_price_impact: 0, sold: 0, remaining: 0 }, // Hết hàng
                ]
            },
            {
                id: 'p4',
                name: 'Tai Nghe Sony WH-1000XM5',
                basePrice: 7000000,
                image: 'https://placehold.co/400x400/800080/FFFFFF?text=Sony+XM5',
                description: 'Tai nghe chống ồn hàng đầu với chất lượng âm thanh tuyệt vời.',
                options: {
                    color: [
                        { name: 'Đen', display_image: 'https://placehold.co/400x400/000000/FFFFFF?text=Den', price_impact: 0 },
                        { name: 'Bạc', display_image: 'https://placehold.co/400x400/C0C0C0/000000?text=Bac', price_impact: 0 }
                    ]
                },
                variants: [
                    { color: 'Đen', storage: null, additional_price_impact: 0, sold: 20, remaining: 10 },
                    { color: 'Bạc', storage: null, additional_price_impact: 0, sold: 15, remaining: 5 }
                ]
            }
        ];
        localStorage.setItem('products', JSON.stringify(products));

        // Dữ liệu voucher mẫu (hoặc tải từ localStorage)
        let vouchers = JSON.parse(localStorage.getItem('vouchers')) || {
            'SALE10': 0.10, // Giảm 10%
            'GIAM50K': 50000, // Giảm 50,000 VND
            'FREESHIP': 'freeship' // Voucher miễn phí vận chuyển
        };
        localStorage.setItem('vouchers', JSON.stringify(vouchers));

        // Dữ liệu cài đặt cửa hàng (hoặc tải từ localStorage)
        let shopSettings = JSON.parse(localStorage.getItem('shopSettings')) || {
            shopName: 'Cửa Hàng Di Động',
            backgroundImage: '',
            shopAddress: '' // New: Shop address
        };
        localStorage.setItem('shopSettings', JSON.stringify(shopSettings));


        // Dữ liệu đơn hàng
        let orders = JSON.parse(localStorage.getItem('orders')) || [];
        // Add some sample shipping history for testing
        if (orders.filter(o => o.status === 'shipping').length === 0 && products.length > 0) {
            orders.push({
                id: 'ORD-TEST1',
                product: {
                    id: products[0].id,
                    name: products[0].name,
                    selectedOptions: products[0].variants[0] ? { color: products[0].variants[0].color, storage: products[0].variants[0].storage } : null,
                    priceAtOrder: products[0].basePrice + (products[0].variants[0]?.additional_price_impact || 0)
                },
                customer: { name: 'Nguyễn Văn A', phone: '0912345678', address: '123 Đường ABC, Quận 1, TP.HCM' },
                createdAt: new Date(Date.now() - 2 * 24 * 60 * 60 * 1000).toISOString(), // 2 days ago
                status: 'shipping',
                shippingHistory: [
                    { date: new Date(Date.now() - 2 * 24 * 60 * 60 * 1000).toISOString(), location: 'Kho HN' },
                    { date: new Date(Date.now() - 1 * 24 * 60 * 60 * 1000).toISOString(), location: 'Đang vận chuyển tại Đà Nẵng' }
                ],
                expectedDeliveryDate: new Date(Date.now() + 3 * 24 * 60 * 60 * 1000).toISOString() // 3 days from now
            });
            localStorage.setItem('orders', JSON.stringify(orders));
        }

        let currentSelectedProduct = null;
        let currentSelectedOptions = { color: null, storage: null }; // Stores selected option NAMES { color: 'Titan Tự Nhiên', storage: '128GB' }
        let currentCalculatedPrice = 0; // This will hold the basePrice + variant_price_impact
        let currentDiscountedPrice = 0; // This will hold the price after voucher

        // --- DOM Elements ---
        const pageTitle = document.getElementById('page-title'); // For browser tab title
        const shopNameDisplay = document.getElementById('shop-name-display'); // For header display
        const productGrid = document.getElementById('product-grid');
        const productDetailModal = document.getElementById('product-detail-modal');
        const closeProductModalBtn = document.getElementById('close-product-modal');
        const modalProductName = document.getElementById('modal-product-name');
        const modalProductImage = document.getElementById('modal-product-image');
        const modalProductBasePrice = document.getElementById('modal-product-base-price'); // New element for base price
        const modalProductPriceDisplay = document.getElementById('modal-product-price-display'); // This will be total price
        const modalProductDiscountDisplay = document.getElementById('modal-product-discount-display');
        const modalProductSold = document.getElementById('modal-product-sold'); // Quantity for selected variant
        const modalProductRemaining = document.getElementById('modal-product-remaining'); // Quantity for selected variant
        const modalProductDescription = document.getElementById('modal-product-description');
        const modalShippingDisplay = document.getElementById('modal-shipping-display');
        const productOptionsDiv = document.getElementById('product-options');
        const voucherCodeInput = document.getElementById('voucher-code');
        const applyVoucherBtn = document.getElementById('apply-voucher-btn');
        const createOrderButton = document.getElementById('create-order-button');

        const orderCreationModal = document.getElementById('order-creation-modal');
        const closeOrderModalBtn = document.getElementById('close-order-modal');
        const orderIdDisplay = document.getElementById('order-id-display');
        const orderForm = document.getElementById('order-form');
        const customerNameInput = document.getElementById('customer-name');
        const customerPhoneInput = document.getElementById('customer-phone');
        const customerAddressInput = document.getElementById('customer-address');
        const orderStatusSteps = document.querySelectorAll('.order-status-step');

        const showProductsBtn = document.getElementById('show-products-btn');
        const showCreatedOrdersBtn = document.getElementById('show-created-orders-btn');
        const showShippingOrdersBtn = document.getElementById('show-shipping-orders-btn');
        const showDeliveredOrdersBtn = document.getElementById('show-delivered-orders-btn');
        const openManagementModalBtn = document.getElementById('open-management-modal-btn');
        const openSettingsModalBtn = document.getElementById('open-settings-modal-btn');
        const openShopAnalyticsModalBtn = document.getElementById('open-shop-analytics-modal-btn'); // New management button
        const shopAddressDisplay = document.getElementById('shop-address-display'); // Shop address display in header
        const openAddressInMapBtn = document.getElementById('open-address-in-map-btn'); // Button to open map from header

        const productListSection = document.getElementById('product-list-section');
        const createdOrdersSection = document.getElementById('created-orders-section');
        const shippingOrdersSection = document.getElementById('shipping-orders-section');
        const deliveredOrdersSection = document.getElementById('delivered-orders-section');

        const createdOrdersList = document.getElementById('created-orders-list');
        const shippingOrdersList = document.getElementById('shipping-orders-list');
        const deliveredOrdersList = document.getElementById('delivered-orders-list');

        // Search inputs for order lists
        const searchCreatedOrdersInput = document.getElementById('search-created-orders');
        const clearSearchCreatedOrdersBtn = document.getElementById('clear-search-created-orders');
        const searchShippingOrdersInput = document.getElementById('search-shipping-orders');
        const clearSearchShippingOrdersBtn = document.getElementById('clear-search-shipping-orders');
        const searchDeliveredOrdersInput = document.getElementById('search-delivered-orders');
        const clearSearchDeliveredOrdersBtn = document.getElementById('clear-search-delivered-orders');


        // Shop Management Modal Elements
        const shopManagementModal = document.getElementById('shop-management-modal');
        const closeManagementModalBtn = document.getElementById('close-management-modal');
        const addEditProductForm = document.getElementById('add-edit-product-form');
        const editProductIdInput = document.getElementById('edit-product-id');
        const addEditProductTitle = document.getElementById('add-edit-product-title');
        const newProductNameInput = document.getElementById('new-product-name');
        const newProductBasePriceInput = document.getElementById('new-product-base-price');
        const newProductImageInput = document.getElementById('new-product-image');
        const newProductDescriptionInput = document.getElementById('new-product-description');
        const colorOptionsContainer = document.getElementById('color-options-container');
        const addColorOptionBtn = document.getElementById('add-color-option-btn');
        const storageOptionsContainer = document.getElementById('storage-options-container');
        const addStorageOptionBtn = document.getElementById('add-storage-option-btn');
        const variantsContainer = document.getElementById('variants-container'); // New container for variants
        const addVariantBtn = document.getElementById('add-variant-btn'); // New button for variants
        const submitProductBtn = document.getElementById('submit-product-btn');
        const cancelEditBtn = document.getElementById('cancel-edit-btn');

        const productManagementList = document.getElementById('product-management-list');
        const uploadMainImageBtn = document.getElementById('upload-main-image-btn');

        const addVoucherForm = document.getElementById('add-voucher-form');
        const newVoucherCodeInput = document.getElementById('new-voucher-code');
        const newVoucherValueInput = document.getElementById('new-voucher-value');
        const currentVouchersList = document.getElementById('current-vouchers-list'); // New list for current vouchers


        // Shop Analytics Modal Elements (NEW)
        const shopAnalyticsModal = document.getElementById('shop-analytics-modal');
        const closeAnalyticsModal = document.getElementById('close-analytics-modal');
        const reportStartDateInput = document.getElementById('report-start-date');
        const reportEndDateInput = document.getElementById('report-end-date');
        const viewAnalyticsReportBtn = document.getElementById('view-analytics-report-btn');
        const revenueDisplay = document.getElementById('revenue-display');
        const salesQuantityDisplay = document.getElementById('sales-quantity-display');
        const inboundQuantityDisplay = document.getElementById('inbound-quantity-display');
        const shopAddressSettingsInput = document.getElementById('shop-address-settings-input');
        const openGoogleMapsBtn = document.getElementById('open-google-maps-btn');
        const saveShopAddressBtn = document.getElementById('save-shop-address-btn');


        // --- Functions ---

        // Hàm hiển thị thông báo
        function showMessage(message, type = 'info') {
            const messageBox = document.getElementById('message-box');
            messageBox.textContent = message;
            messageBox.className = `hidden fixed bottom-6 right-6 px-6 py-3 rounded-lg shadow-xl z-50 text-lg`; // Reset classes
            if (type === 'success') {
                messageBox.classList.add('bg-green-600');
            } else if (type === 'error') {
                messageBox.classList.add('bg-red-600');
            } else {
                messageBox.classList.add('bg-blue-600');
            }
            messageBox.classList.remove('hidden');
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 3000); // Hide after 3 seconds
        }

        // Hàm định dạng tiền tệ
        function formatCurrency(amount) {
            return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount);
        }

        // Add/remove active class for modal overlay
        function toggleModalActive(modalElement, show) {
            if (show) {
                modalElement.classList.remove('hidden');
                setTimeout(() => modalElement.classList.add('active'), 10); // Small delay for transition
            } else {
                modalElement.classList.remove('active');
                modalElement.addEventListener('transitionend', () => {
                    modalElement.classList.add('hidden');
                }, { once: true });
            }
        }

        // Render danh sách sản phẩm
        function renderProducts() {
            productGrid.innerHTML = ''; // Xóa sản phẩm cũ
            products.forEach(product => {
                // Calculate total sold and remaining across all variants for the product card
                const totalSold = product.variants ? product.variants.reduce((sum, variant) => sum + (variant.sold || 0), 0) : 0;
                const totalRemaining = product.variants ? product.variants.reduce((sum, variant) => sum + (variant.remaining || 0), 0) : 0;

                const productCard = document.createElement('div');
                productCard.className = 'product-card bg-gray-50 p-6 rounded-xl shadow-lg hover:shadow-xl transform hover:-translate-y-1 transition-all duration-300 cursor-pointer border-2 border-transparent hover:border-blue-300';
                productCard.dataset.productId = product.id;
                productCard.innerHTML = `
                    <img src="${product.image}" onerror="this.src='https://placehold.co/400x400/CCCCCC/000000?text=No+Image';" alt="${product.name}" class="w-full h-48 object-contain mb-4 rounded-lg shadow-sm">
                    <h3 class="text-xl font-semibold mb-2 text-gray-800">${product.name}</h3>
                    <p class="text-lg font-bold text-green-700 mb-2">${formatCurrency(product.basePrice)}</p>
                    <p class="text-sm text-gray-600">Đã bán: ${totalSold}</p>
                    <p class="text-sm text-gray-600">Còn lại: <span class="${totalRemaining === 0 ? 'out-of-stock' : ''}">${totalRemaining}</span></p>
                    <button class="select-product-btn mt-4 bg-blue-500 hover:bg-blue-600 text-white font-medium py-2 px-4 rounded-lg w-full transition-all duration-200 shadow-md">
                        <i class="fas fa-info-circle mr-2"></i>Chi tiết
                    </button>
                `;
                productGrid.appendChild(productCard);
            });

            // Gắn sự kiện click cho nút "Chi tiết"
            document.querySelectorAll('.select-product-btn').forEach(button => {
                button.addEventListener('click', (event) => {
                    const productId = event.target.closest('.product-card').dataset.productId;
                    openProductDetailModal(productId);
                });
            });
        }

        // Mở modal chi tiết sản phẩm
        function openProductDetailModal(productId) {
            currentSelectedProduct = products.find(p => p.id === productId);
            if (!currentSelectedProduct) return;

            modalProductName.textContent = currentSelectedProduct.name;
            modalProductImage.src = currentSelectedProduct.image;
            modalProductBasePrice.textContent = formatCurrency(currentSelectedProduct.basePrice);
            modalProductDescription.textContent = currentSelectedProduct.description || 'Chưa có mô tả.';
            
            // Reset quantity displays initially
            modalProductSold.textContent = 'N/A';
            modalProductSold.classList.remove('out-of-stock');
            modalProductRemaining.textContent = 'N/A';
            modalProductRemaining.classList.remove('out-of-stock');
            createOrderButton.disabled = true; // Disable until a valid variant is selected

            voucherCodeInput.value = ''; // Clear voucher input
            modalShippingDisplay.classList.add('hidden'); // Hide freeship display
            modalShippingDisplay.textContent = ''; // Clear freeship content

            currentSelectedOptions = { color: null, storage: null }; // Reset selected options to null

            // Initial price display (base price only)
            currentCalculatedPrice = currentSelectedProduct.basePrice;
            currentDiscountedPrice = currentCalculatedPrice;
            modalProductPriceDisplay.classList.remove('price-original');
            modalProductDiscountDisplay.classList.add('hidden');
            modalProductPriceDisplay.textContent = formatCurrency(currentCalculatedPrice);

            renderProductOptions(currentSelectedProduct.options);

            toggleModalActive(productDetailModal, true);
        }

        // Đóng modal chi tiết sản phẩm
        function closeProductDetailModal() {
            toggleModalActive(productDetailModal, false);
        }

        // Function to find the matching variant based on selected options
        function findMatchingVariant(product, selectedColorName, selectedStorageName) {
            if (!product || !product.variants) return null;

            return product.variants.find(variant => {
                const colorMatch = (variant.color === selectedColorName);
                const storageMatch = (variant.storage === selectedStorageName);
                return colorMatch && storageMatch;
            });
        }

        // Function to get price impact for a given option name and type
        function getOptionPriceImpact(productOptions, type, name) {
            if (!productOptions || !productOptions[type] || !name) return 0;
            const option = productOptions[type].find(opt => opt.name === name);
            return option ? (option.price_impact || 0) : 0;
        }


        // Function to update the price and quantity displayed in the modal based on selected options
        function updateModalQuantityAndPriceDisplay() {
            const selectedColorName = currentSelectedOptions.color;
            const selectedStorageName = currentSelectedOptions.storage;

            let selectedVariant = null;
            let colorPriceImpact = 0;
            let storagePriceImpact = 0;
            let additionalVariantPriceImpact = 0;

            if (currentSelectedProduct) {
                // Get individual option price impacts
                colorPriceImpact = getOptionPriceImpact(currentSelectedProduct.options, 'color', selectedColorName);
                storagePriceImpact = getOptionPriceImpact(currentSelectedProduct.options, 'storage', selectedStorageName);

                // Find the variant
                if (currentSelectedProduct.options.color && currentSelectedProduct.options.storage) {
                    if (selectedColorName && selectedStorageName) {
                         selectedVariant = findMatchingVariant(currentSelectedProduct, selectedColorName, selectedStorageName);
                    }
                } else if (currentSelectedProduct.options.color && !currentSelectedProduct.options.storage) {
                    if (selectedColorName) {
                        selectedVariant = findMatchingVariant(currentSelectedProduct, selectedColorName, null);
                    }
                } else if (!currentSelectedProduct.options.color && currentSelectedProduct.options.storage) {
                     if (selectedStorageName) {
                        selectedVariant = findMatchingVariant(currentSelectedProduct, null, selectedStorageName);
                    }
                } else {
                    // Product has no options at all, assume first variant
                    selectedVariant = currentSelectedProduct.variants && currentSelectedProduct.variants.length > 0 ? currentSelectedProduct.variants[0] : null;
                }

                if (selectedVariant) {
                    additionalVariantPriceImpact = selectedVariant.additional_price_impact || 0;
                }
            }
           
            // Calculate total price
            currentCalculatedPrice = currentSelectedProduct.basePrice + colorPriceImpact + storagePriceImpact + additionalVariantPriceImpact;
            modalProductPriceDisplay.textContent = formatCurrency(currentCalculatedPrice);
            currentDiscountedPrice = currentCalculatedPrice; // Reset discounted price to new calculated price

            // Reset quantity display for "N/A" and disable button
            modalProductSold.textContent = 'N/A';
            modalProductSold.classList.remove('out-of-stock');
            modalProductRemaining.textContent = 'N/A';
            modalProductRemaining.classList.remove('out-of-stock');
            createOrderButton.disabled = true;

            if (selectedVariant) {
                // Update quantity display
                modalProductSold.textContent = selectedVariant.sold;
                modalProductRemaining.textContent = selectedVariant.remaining;

                modalProductRemaining.classList.remove('out-of-stock');
                if (selectedVariant.remaining === 0) {
                    modalProductRemaining.textContent = '0 (Hết hàng)';
                    modalProductRemaining.classList.add('out-of-stock');
                    createOrderButton.disabled = true; // Disable if out of stock
                    showMessage('Sản phẩm này đã hết hàng!', 'error');
                } else {
                    createOrderButton.disabled = false; // Enable if in stock
                }
            } else {
                // If no variant is selected (e.g., incomplete options)
                let hasColorOptions = currentSelectedProduct.options.color && currentSelectedProduct.options.color.length > 0;
                let hasStorageOptions = currentSelectedProduct.options.storage && currentSelectedProduct.options.storage.length > 0;

                if ((hasColorOptions && !selectedColorName) || (hasStorageOptions && !selectedStorageName)) {
                     // Only show N/A if options are expected but not fully selected
                     modalProductSold.textContent = 'N/A';
                     modalProductRemaining.textContent = 'N/A';
                     createOrderButton.disabled = true; // Disable if options not fully selected
                } else {
                    // For products with no options at all (and thus no variants to select)
                    // The 'selectedVariant' would be the first one if present and no options.
                    // If no options and no variants, it still needs to be handled.
                    // This part is mainly for enabling/disabling button if no variants are found.
                    if (currentSelectedProduct.variants && currentSelectedProduct.variants.length > 0) {
                         createOrderButton.disabled = false;
                    } else {
                        // Product has no options and no defined variants - should probably not happen if product is valid.
                        createOrderButton.disabled = true;
                        showMessage('Không có biến thể nào cho sản phẩm này.', 'error');
                    }
                }
            }
            
            // Re-apply voucher if there was one
            const voucherCode = voucherCodeInput.value.trim().toUpperCase();
            if (vouchers[voucherCode] !== undefined) {
                applyVoucherLogic(voucherCode);
            }
        }


        // Render các tùy chọn sản phẩm (màu sắc, dung lượng)
        function renderProductOptions(options) {
            productOptionsDiv.innerHTML = '';
            
            // Auto-select first option if available and no initial selection
            let hasColorOptions = options && options.color && options.color.length > 0;
            let hasStorageOptions = options && options.storage && options.storage.length > 0;

            if (hasColorOptions) {
                const colorGroup = document.createElement('div');
                colorGroup.className = 'mb-4';
                colorGroup.innerHTML = `<label class="block text-gray-700 text-lg font-bold mb-2">Chọn Màu Sắc:</label>`;
                const colorButtonsContainer = document.createElement('div');
                colorButtonsContainer.className = 'flex flex-wrap gap-3';

                options.color.forEach((optionValue, index) => {
                    const optionButton = document.createElement('button');
                    optionButton.className = 'option-button p-3 rounded-lg border-2 border-gray-300 text-gray-700 font-medium hover:border-blue-500 transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500';
                    
                    const colorDisplay = optionValue.name;
                    const textColor = (optionValue.name === 'Đen Phantom' || optionValue.name === 'Titan Xanh' || optionValue.name === 'Xám Không Gian' || optionValue.name === 'Đen') ? 'white' : 'black';
                    optionButton.innerHTML = `<span class="inline-block w-5 h-5 rounded-full border border-gray-400 mr-2" style="background-color: ${colorDisplay};"></span><span style="color: ${textColor}">${optionValue.name}</span>`;
                    optionButton.dataset.type = 'color';
                    optionButton.dataset.name = optionValue.name;
                    optionButton.dataset.image = optionValue.display_image || '';

                    optionButton.addEventListener('click', () => {
                        colorButtonsContainer.querySelectorAll('.option-button').forEach(btn => {
                            btn.classList.remove('selected', 'bg-blue-100', 'border-blue-600');
                        });
                        optionButton.classList.add('selected', 'bg-blue-100', 'border-blue-600');
                        currentSelectedOptions.color = optionValue.name;
                        modalProductImage.src = optionValue.display_image || currentSelectedProduct.image; // Update main image if color has specific image
                        updateModalQuantityAndPriceDisplay();
                    });
                    colorButtonsContainer.appendChild(optionButton);

                    // Auto-select the first color
                    if (index === 0 && !currentSelectedOptions.color) {
                        optionButton.click();
                    }
                });
                colorGroup.appendChild(colorButtonsContainer);
                productOptionsDiv.appendChild(colorGroup);
            }

            if (hasStorageOptions) {
                const storageGroup = document.createElement('div');
                storageGroup.className = 'mb-4';
                storageGroup.innerHTML = `<label class="block text-gray-700 text-lg font-bold mb-2">Chọn Dung Lượng:</label>`;
                const storageButtonsContainer = document.createElement('div');
                storageButtonsContainer.className = 'flex flex-wrap gap-3';

                options.storage.forEach((optionValue, index) => {
                    const optionButton = document.createElement('button');
                    optionButton.className = 'option-button p-3 rounded-lg border-2 border-gray-300 text-gray-700 font-medium hover:border-blue-500 transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-blue-500';
                    
                    optionButton.textContent = optionValue.name; 
                    optionButton.dataset.type = 'storage';
                    optionButton.dataset.name = optionValue.name;

                    optionButton.addEventListener('click', () => {
                        storageButtonsContainer.querySelectorAll('.option-button').forEach(btn => {
                            btn.classList.remove('selected', 'bg-blue-100', 'border-blue-600');
                        });
                        optionButton.classList.add('selected', 'bg-blue-100', 'border-blue-600');
                        currentSelectedOptions.storage = optionValue.name;
                        updateModalQuantityAndPriceDisplay();
                    });
                    storageButtonsContainer.appendChild(optionButton);

                    // Auto-select the first storage
                    if (index === 0 && !currentSelectedOptions.storage) {
                        optionButton.click();
                    }
                });
                storageGroup.appendChild(storageButtonsContainer);
                productOptionsDiv.appendChild(storageGroup);
            }

            // If a product has no options (e.g., headphones), ensure quantity is shown and button enabled
            if (!hasColorOptions && !hasStorageOptions) {
                updateModalQuantityAndPriceDisplay(); // Call it once to init quantity/price
            }
        }

        // Helper function for voucher logic (reusable)
        function applyVoucherLogic(voucherCode) {
            const voucherValue = vouchers[voucherCode];
            let finalPrice = currentCalculatedPrice; // Apply voucher to the current calculated price

            modalShippingDisplay.classList.add('hidden'); // Hide freeship display by default
            modalShippingDisplay.textContent = ''; // Clear freeship content

            if (typeof voucherValue === 'number') {
                if (voucherValue < 1) { // Percentage discount
                    finalPrice = currentCalculatedPrice * (1 - voucherValue);
                    showMessage(`Áp dụng voucher ${voucherCode}: Giảm ${voucherValue * 100}% thành công!`, 'success');
                } else { // Fixed amount discount
                    finalPrice = currentCalculatedPrice - voucherValue;
                    showMessage(`Áp dụng voucher ${voucherCode}: Giảm ${formatCurrency(voucherValue)} thành công!`, 'success');
                }
            } else if (voucherValue === 'freeship') {
                showMessage(`Áp dụng voucher ${voucherCode}: Miễn phí vận chuyển thành công!`, 'success');
                modalShippingDisplay.classList.remove('hidden'); // Show freeship display
                modalShippingDisplay.textContent = 'Phí vận chuyển: Miễn Phí';
            }

            currentDiscountedPrice = Math.max(0, finalPrice);
            modalProductPriceDisplay.classList.add('price-original');
            modalProductDiscountDisplay.classList.remove('hidden');
            modalProductDiscountDisplay.textContent = `Giá sau ưu đãi: ${formatCurrency(currentDiscountedPrice)}`;
        }


        // Áp dụng voucher
        applyVoucherBtn.addEventListener('click', () => {
            const voucherCode = voucherCodeInput.value.trim().toUpperCase();
            if (vouchers[voucherCode] !== undefined) {
                applyVoucherLogic(voucherCode);
            } else {
                showMessage('Mã voucher không hợp lệ!', 'error');
                // If invalid voucher, reset price display to current calculated price
                modalProductPriceDisplay.classList.remove('price-original');
                modalProductDiscountDisplay.classList.add('hidden');
                modalProductPriceDisplay.textContent = formatCurrency(currentCalculatedPrice);
                currentDiscountedPrice = currentCalculatedPrice;
                modalShippingDisplay.classList.add('hidden'); // Hide freeship display
                modalShippingDisplay.textContent = ''; // Clear freeship content
            }
        });

        // Mở modal tạo đơn hàng
        createOrderButton.addEventListener('click', () => {
            if (!currentSelectedProduct) {
                showMessage('Vui lòng chọn một sản phẩm trước khi tạo đơn hàng!', 'error');
                return;
            }
            
            // Find the specific variant to check remaining quantity
            const selectedVariant = findMatchingVariant(currentSelectedProduct, currentSelectedOptions.color, currentSelectedOptions.storage);

            if (!selectedVariant) {
                showMessage('Vui lòng chọn đầy đủ các tùy chọn cho sản phẩm!', 'error');
                return;
            }

            if (selectedVariant.remaining <= 0) {
                showMessage('Sản phẩm đã hết hàng!', 'error');
                return;
            }

            closeProductDetailModal();
            orderIdDisplay.textContent = generateOrderId();
            orderForm.reset();

            // Reset phone and address fields and their visibility state
            customerPhoneInput.type = 'tel';
            document.querySelector('#customer-phone').value = ''; // Clear value
            document.querySelector('#customer-phone + .toggle-visibility-btn i').classList.remove('fa-eye-slash');
            document.querySelector('#customer-phone + .toggle-visibility-btn i').classList.add('fa-eye');

            customerAddressInput.classList.remove('blurred-content');
            document.querySelector('#customer-address').value = ''; // Clear value
            document.querySelector('#customer-address + .toggle-visibility-btn i').classList.remove('fa-eye-slash');
            document.querySelector('#customer-address + .toggle-visibility-btn i').classList.add('fa-eye');


            orderStatusSteps.forEach(step => step.classList.remove('active'));
            document.querySelector('.order-status-step[data-status="created"]').classList.add('active');

            toggleModalActive(orderCreationModal, true);
        });

        // Đóng modal tạo đơn hàng
        function closeOrderCreationModal() {
            toggleModalActive(orderCreationModal, false);
        }

        // Tạo mã đơn hàng duy nhất
        function generateOrderId() {
            let newId;
            let isUnique = false;
            while (!isUnique) {
                newId = 'ORD-' + Math.random().toString(36).substring(2, 11).toUpperCase();
                isUnique = !orders.some(order => order.id === newId);
            }
            return newId;
        }

        // Xử lý khi submit form tạo đơn hàng
        orderForm.addEventListener('submit', (event) => {
            event.preventDefault();

            const orderId = orderIdDisplay.textContent;
            const customerName = customerNameInput.value.trim();
            const customerPhone = customerPhoneInput.value.trim(); // Get actual value
            const customerAddress = customerAddressInput.value.trim(); // Get actual value

            if (!customerName || !customerPhone || !customerAddress) {
                showMessage('Vui lòng điền đầy đủ thông tin khách hàng!', 'error');
                return;
            }

            // Find the product and the specific variant to update quantities
            const productIndex = products.findIndex(p => p.id === currentSelectedProduct.id);
            if (productIndex === -1) {
                showMessage('Lỗi: Không tìm thấy sản phẩm.', 'error');
                return;
            }

            const selectedVariant = findMatchingVariant(products[productIndex], currentSelectedOptions.color, currentSelectedOptions.storage);
            if (!selectedVariant) {
                showMessage('Lỗi: Không tìm thấy biến thể sản phẩm đã chọn.', 'error');
                return;
            }

            if (selectedVariant.remaining <= 0) {
                 showMessage('Sản phẩm đã hết hàng! Vui lòng chọn biến thể khác.', 'error');
                 return;
            }

            // Update quantities for the specific variant
            selectedVariant.sold++;
            selectedVariant.remaining--;

            const newOrder = {
                id: orderId,
                product: {
                    id: currentSelectedProduct.id,
                    name: currentSelectedProduct.name,
                    // Store the selected options NAMES for the order
                    selectedOptions: JSON.parse(JSON.stringify(currentSelectedOptions)), 
                    priceAtOrder: currentDiscountedPrice // Use the final discounted price
                },
                customer: {
                    name: customerName,
                    phone: customerPhone,
                    address: customerAddress
                },
                createdAt: new Date().toISOString(),
                status: 'created',
                // New fields for tracking
                shippingHistory: [],
                expectedDeliveryDate: ''
            };

            orders.push(newOrder);
            localStorage.setItem('orders', JSON.stringify(orders));
            localStorage.setItem('products', JSON.stringify(products)); // Save updated product quantities

            showMessage('Đơn hàng đã được tạo thành công!', 'success');
            renderProducts(); // Re-render product list to reflect new quantities
            closeOrderCreationModal();
            updateOrderLists();
            showCreatedOrdersBtn.click();
        });

        // Cập nhật trạng thái đơn hàng
        function updateOrderStatus(orderId, newStatus) {
            const orderIndex = orders.findIndex(order => order.id === orderId);
            if (orderIndex > -1) {
                orders[orderIndex].status = newStatus;
                // Initialize shipping history if moving to 'shipping' for the first time
                if (newStatus === 'shipping' && (!orders[orderIndex].shippingHistory || orders[orderIndex].shippingHistory.length === 0)) {
                    orders[orderIndex].shippingHistory = [{
                        date: new Date().toISOString(),
                        location: 'Đã xuất kho'
                    }];
                    // Set a default expected delivery date (e.g., 5 days from now)
                    orders[orderIndex].expectedDeliveryDate = new Date(Date.now() + 5 * 24 * 60 * 60 * 1000).toISOString();
                }
                localStorage.setItem('orders', JSON.stringify(orders));
                showMessage(`Trạng thái đơn hàng ${orderId} đã được cập nhật thành "${newStatus}"`, 'info');
                updateOrderLists();
            }
        }

        // Xóa đơn hàng
        function deleteOrder(orderId) {
            orders = orders.filter(order => order.id !== orderId);
            localStorage.setItem('orders', JSON.stringify(orders));
            showMessage(`Đơn hàng ${orderId} đã được xóa thành công!`, 'success');
            updateOrderLists();
        }

        // Hàm định dạng ngày
        function formatDate(dateString) {
            if (!dateString) return 'Chưa xác định';
            const date = new Date(dateString);
            return date.toLocaleDateString('vi-VN', {
                year: 'numeric', month: '2-digit', day: '2-digit'
            });
        }

        // Hàm định dạng ngày giờ
        function formatDateTime(dateString) {
            if (!dateString) return 'Chưa xác định';
            const date = new Date(dateString);
            return date.toLocaleDateString('vi-VN', {
                year: 'numeric', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit'
            });
        }

        // Render danh sách đơn hàng theo trạng thái
        function renderOrderList(containerElement, status, searchTerm = '') {
            containerElement.innerHTML = '';
            const normalizedSearchTerm = searchTerm.toLowerCase();

            const filteredOrders = orders.filter(order => {
                const orderData = `${order.id} ${order.product.name} ${order.customer.name} ${order.customer.phone} ${order.customer.address}`.toLowerCase();
                const matchesStatus = order.status === status;
                const matchesSearch = orderData.includes(normalizedSearchTerm);
                return matchesStatus && matchesSearch;
            });

            if (filteredOrders.length === 0) {
                containerElement.innerHTML = `<p class="text-gray-500 italic">Chưa có đơn hàng nào ở trạng thái này hoặc không tìm thấy kết quả phù hợp.</p>`;
                return;
            }

            filteredOrders.forEach(order => {
                const orderDate = formatDateTime(order.createdAt);
                let optionsString = '';
                const selectedColor = order.product.selectedOptions?.color;
                const selectedStorage = order.product.selectedOptions?.storage;

                if (selectedColor && selectedStorage) {
                    optionsString = `Màu: ${selectedColor}, Dung lượng: ${selectedStorage}`;
                } else if (selectedColor) {
                    optionsString = `Màu: ${selectedColor}`;
                } else if (selectedStorage) {
                    optionsString = `Dung lượng: ${selectedStorage}`;
                }

                let shippingInfoHtml = '';
                if (status === 'shipping') {
                    const currentShippingLocation = order.shippingHistory && order.shippingHistory.length > 0 ?
                                                    order.shippingHistory[order.shippingHistory.length - 1].location : 'Chưa có thông tin';
                    const expectedDelivery = order.expectedDeliveryDate ? formatDate(order.expectedDeliveryDate) : 'Chưa xác định';

                    shippingInfoHtml = `
                        <p class="text-gray-700 mt-2"><i class="fas fa-truck mr-2"></i>Vị trí hiện tại: <span class="font-semibold">${currentShippingLocation}</span></p>
                        <p class="text-gray-700"><i class="fas fa-calendar-alt mr-2"></i>Dự kiến nhận hàng: <span class="font-semibold">${expectedDelivery}</span></p>
                        <button class="toggle-shipping-details-btn bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded-lg text-sm mt-2 transition-all duration-200 shadow-md">
                            <i class="fas fa-chevron-down mr-1"></i>Xem chi tiết
                        </button>
                        <div class="order-expandable-content mt-3 p-3 bg-gray-100 rounded-lg hidden">
                            <h5 class="font-semibold mb-2">Lịch sử vận chuyển:</h5>
                            <ul class="list-disc list-inside mb-4 text-sm">
                                ${order.shippingHistory ? order.shippingHistory.map(entry => `<li>${formatDateTime(entry.date)}: ${entry.location}</li>`).join('') : 'Không có lịch sử.'}
                            </ul>
                            <div class="space-y-3">
                                <div>
                                    <label class="block text-gray-700 text-sm font-bold mb-1">Cập nhật vị trí hiện tại:</label>
                                    <input type="text" class="current-location-input shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" value="${currentShippingLocation}">
                                </div>
                                <div>
                                    <label class="block text-gray-700 text-sm font-bold mb-1">Cập nhật ngày dự kiến nhận hàng:</label>
                                    <input type="date" class="expected-date-input shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500" value="${order.expectedDeliveryDate ? order.expectedDeliveryDate.substring(0, 10) : ''}">
                                </div>
                                <button class="update-shipping-info-btn bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded-lg text-sm transition-all duration-200 shadow-md w-full">
                                    <i class="fas fa-save mr-1"></i>Cập nhật thông tin vận chuyển
                                </button>
                            </div>
                        </div>
                    `;
                }

                const orderItem = document.createElement('div');
                orderItem.className = 'bg-gray-50 p-5 rounded-lg shadow-sm border border-gray-200';
                orderItem.dataset.orderId = order.id; // Set order ID for easy retrieval
                orderItem.innerHTML = `
                    <h4 class="text-xl font-semibold text-gray-800 mb-2">${order.product.name}</h4>
                    <p class="text-gray-700">Mã đơn hàng: <span class="font-bold text-blue-700">${order.id}</span></p>
                    <p class="text-gray-700">Ngày tạo: ${orderDate}</p>
                    <p class="text-gray-700">Giá: ${formatCurrency(order.product.priceAtOrder)}</p>
                    ${optionsString ? `<p class="text-gray-700">Tùy chọn: ${optionsString}</p>` : ''}
                    <p class="text-gray-700">Khách hàng: ${order.customer.name}</p>
                    <p class="text-gray-700">Điện thoại: ${order.customer.phone}</p>
                    <p class="text-gray-700">Địa chỉ: ${order.customer.address}</p>
                    ${shippingInfoHtml}
                    <div class="mt-4 flex flex-wrap gap-2">
                        ${status === 'created' ? `<button class="move-to-shipping-btn bg-yellow-500 hover:bg-yellow-600 text-white px-4 py-2 rounded-lg text-sm transition-all duration-200 shadow-md" data-order-id="${order.id}"><i class="fas fa-shipping-fast mr-2"></i>Bắt Đầu Vận Chuyển</button>` : ''}
                        ${status === 'shipping' ? `<button class="move-to-delivered-btn bg-green-500 hover:bg-green-600 text-white px-4 py-2 rounded-lg text-sm transition-all duration-200 shadow-md" data-order-id="${order.id}"><i class="fas fa-box-open mr-2"></i>Xác Nhận Đã Giao Hàng</button>` : ''}
                        ${status === 'delivered' ? `<button class="delete-order-btn bg-red-500 hover:bg-red-600 text-white px-4 py-2 rounded-lg text-sm transition-all duration-200 shadow-md" data-order-id="${order.id}"><i class="fas fa-trash-alt mr-2"></i>Xóa Đơn Hàng</button>` : ''}
                    </div>
                `;
                containerElement.appendChild(orderItem);
            });

            // Gắn sự kiện cho các nút chuyển trạng thái và nút xóa
            containerElement.querySelectorAll('.move-to-shipping-btn').forEach(btn => {
                btn.addEventListener('click', (event) => {
                    const orderId = event.target.closest('.bg-gray-50').dataset.orderId;
                    updateOrderStatus(orderId, 'shipping');
                    showShippingOrdersBtn.click();
                });
            });
            containerElement.querySelectorAll('.move-to-delivered-btn').forEach(btn => {
                btn.addEventListener('click', (event) => {
                    const orderId = event.target.closest('.bg-gray-50').dataset.orderId;
                    updateOrderStatus(orderId, 'delivered');
                    showDeliveredOrdersBtn.click();
                });
            });
            containerElement.querySelectorAll('.delete-order-btn').forEach(btn => {
                btn.addEventListener('click', (event) => {
                    const orderId = event.target.closest('.bg-gray-50').dataset.orderId;
                    deleteOrder(orderId);
                });
            });

            // Event listeners for order tracking expand/edit
            containerElement.querySelectorAll('.toggle-shipping-details-btn').forEach(btn => {
                btn.addEventListener('click', (event) => {
                    const expandableContent = btn.nextElementSibling;
                    const icon = btn.querySelector('i');
                    expandableContent.classList.toggle('hidden');
                    expandableContent.classList.toggle('expanded');
                    icon.classList.toggle('fa-chevron-down');
                    icon.classList.toggle('fa-chevron-up');
                });
            });

            containerElement.querySelectorAll('.update-shipping-info-btn').forEach(btn => {
                btn.addEventListener('click', (event) => {
                    const orderItem = event.target.closest('.bg-gray-50');
                    const orderId = orderItem.dataset.orderId;
                    const orderIndex = orders.findIndex(o => o.id === orderId);

                    if (orderIndex > -1) {
                        const currentLocationInput = orderItem.querySelector('.current-location-input');
                        const expectedDateInput = orderItem.querySelector('.expected-date-input');

                        const newLocation = currentLocationInput.value.trim();
                        const newExpectedDate = expectedDateInput.value.trim();

                        if (newLocation && newExpectedDate) {
                            // Update the last entry in shipping history or add a new one if different
                            if (orders[orderIndex].shippingHistory && orders[orderIndex].shippingHistory.length > 0) {
                                const lastEntry = orders[orderIndex].shippingHistory[orders[orderIndex].shippingHistory.length - 1];
                                if (lastEntry.location !== newLocation) {
                                    orders[orderIndex].shippingHistory.push({
                                        date: new Date().toISOString(),
                                        location: newLocation
                                    });
                                } else {
                                    // If location is the same, just update the date of the last entry if needed
                                    lastEntry.date = new Date().toISOString(); // Update timestamp of existing location
                                }
                            } else {
                                orders[orderIndex].shippingHistory = [{
                                    date: new Date().toISOString(),
                                    location: newLocation
                                }];
                            }
                            orders[orderIndex].expectedDeliveryDate = newExpectedDate;

                            localStorage.setItem('orders', JSON.stringify(orders));
                            showMessage('Thông tin vận chuyển đã được cập nhật!', 'success');
                            updateOrderLists(); // Re-render to show updated info
                            orderItem.querySelector('.toggle-shipping-details-btn').click(); // Collapse after update
                        } else {
                            showMessage('Vui lòng điền đầy đủ vị trí hiện tại và ngày dự kiến nhận hàng.', 'error');
                        }
                    }
                });
            });
        }

        // Cập nhật tất cả các danh sách đơn hàng
        function updateOrderLists() {
            renderOrderList(createdOrdersList, 'created', searchCreatedOrdersInput.value);
            renderOrderList(shippingOrdersList, 'shipping', searchShippingOrdersInput.value);
            renderOrderList(deliveredOrdersList, 'delivered', searchDeliveredOrdersInput.value);
        }

        // Hàm chuyển đổi giữa các phần (Products, Created Orders, etc.)
        function showSection(sectionId) {
            const sections = [productListSection, createdOrdersSection, shippingOrdersSection, deliveredOrdersSection];
            sections.forEach(section => {
                if (section.id === sectionId) {
                    section.classList.remove('hidden');
                    section.classList.add('active-section');
                } else {
                    section.classList.add('hidden');
                    section.classList.remove('active-section');
                }
            });

            // Cập nhật trạng thái active của các nút sidebar
            document.querySelectorAll('.tab-button').forEach(button => {
                button.classList.remove('active');
            });
            if (sectionId === 'product-list-section') {
                showProductsBtn.classList.add('active');
            } else if (sectionId === 'created-orders-section') {
                showCreatedOrdersBtn.classList.add('active');
            } else if (sectionId === 'shipping-orders-section') {
                showShippingOrdersBtn.classList.add('active');
            } else if (sectionId === 'delivered-orders-section') {
                showDeliveredOrdersBtn.classList.add('active');
            }
        }

        // --- Shop Management Functions ---
        function openShopManagementModal() {
            toggleModalActive(shopManagementModal, true);
            renderManagedProducts();
            resetAddEditProductForm();
            renderCurrentVouchers(); // Display vouchers when management modal opens
        }

        function closeShopManagementModal() {
            toggleModalActive(shopManagementModal, false);
        }

        // Function to generate a unique product ID
        function generateProductId() {
            return 'PROD-' + Math.random().toString(36).substring(2, 9).toUpperCase();
        }

        // Function to render products in the management list
        function renderManagedProducts() {
            productManagementList.innerHTML = '';
            if (products.length === 0) {
                productManagementList.innerHTML = `<p class="text-gray-500 italic">Chưa có sản phẩm nào trong cửa hàng.</p>`;
                return;
            }

            products.forEach(product => {
                const productItem = document.createElement('div');
                productItem.className = 'flex flex-col sm:flex-row items-center justify-between bg-gray-50 p-4 rounded-lg shadow-sm border border-gray-200';
                productItem.innerHTML = `
                    <span class="font-semibold text-gray-800 mb-2 sm:mb-0 sm:mr-4">${product.name} - ${formatCurrency(product.basePrice)}</span>
                    <div class="flex flex-wrap gap-2">
                        <button class="duplicate-product-btn bg-purple-500 hover:bg-purple-600 text-white px-3 py-1 rounded-lg text-sm transition-all duration-200 shadow-md" data-product-id="${product.id}"><i class="fas fa-copy mr-1"></i>Sao chép</button>
                        <button class="edit-product-btn bg-yellow-500 hover:bg-yellow-600 text-white px-3 py-1 rounded-lg text-sm transition-all duration-200 shadow-md" data-product-id="${product.id}"><i class="fas fa-edit mr-1"></i>Sửa</button>
                        <button class="delete-product-management-btn bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded-lg text-sm transition-all duration-200 shadow-md" data-product-id="${product.id}"><i class="fas fa-trash-alt mr-1"></i>Xóa</button>
                    </div>
                `;
                productItem.querySelector('.duplicate-product-btn').addEventListener('click', (event) => duplicateProduct(product.id)); // New listener for duplicate
                productItem.querySelector('.edit-product-btn').addEventListener('click', (event) => editProduct(product.id));
                productItem.querySelector('.delete-product-management-btn').addEventListener('click', (event) => deleteProductFromManagement(product.id));
                productManagementList.appendChild(productItem);
            });
        }

        // Function to set the form into "add new" mode
        function resetAddEditProductForm() {
            addEditProductForm.reset();
            editProductIdInput.value = '';
            addEditProductTitle.textContent = 'Thêm Mặt Hàng Mới';
            submitProductBtn.textContent = 'Thêm Sản Phẩm';
            submitProductBtn.classList.remove('bg-blue-600', 'hover:bg-blue-700');
            submitProductBtn.classList.add('bg-green-600', 'hover:bg-green-700');
            submitProductBtn.innerHTML = '<i class="fas fa-save mr-2"></i>Thêm Sản Phẩm';
            cancelEditBtn.classList.add('hidden');
            colorOptionsContainer.innerHTML = '';
            storageOptionsContainer.innerHTML = '';
            variantsContainer.innerHTML = ''; // Clear variants
        }

        // Function to set the form into "edit" mode
        function editProduct(productId) {
            const productToEdit = products.find(p => p.id === productId);
            if (!productToEdit) {
                showMessage('Không tìm thấy sản phẩm để chỉnh sửa.', 'error');
                return;
            }

            // Populate main product fields
            editProductIdInput.value = productToEdit.id;
            newProductNameInput.value = productToEdit.name;
            newProductBasePriceInput.value = productToEdit.basePrice;
            newProductImageInput.value = productToEdit.image;
            newProductDescriptionInput.value = productToEdit.description;

            // Clear and populate color options (with price_impact)
            colorOptionsContainer.innerHTML = '';
            if (productToEdit.options && productToEdit.options.color) {
                productToEdit.options.color.forEach(color => addColorOption(color.name, color.display_image, color.price_impact));
            }

            // Clear and populate storage options (with price_impact)
            storageOptionsContainer.innerHTML = '';
            if (productToEdit.options && productToEdit.options.storage) {
                productToEdit.options.storage.forEach(storage => addStorageOption(storage.name, storage.price_impact));
            }

            // Clear and populate variants
            variantsContainer.innerHTML = '';
            if (productToEdit.variants) {
                productToEdit.variants.forEach(variant => addVariant(
                    variant.color,
                    variant.storage,
                    variant.additional_price_impact,
                    variant.sold,
                    variant.remaining
                ));
            }


            // Change form title and submit button text
            addEditProductTitle.textContent = 'Chỉnh Sửa Mặt Hàng';
            submitProductBtn.textContent = 'Cập Nhật Sản Phẩm';
            submitProductBtn.classList.remove('bg-green-600', 'hover:bg-green-700');
            submitProductBtn.classList.add('bg-blue-600', 'hover:bg-blue-700');
            submitProductBtn.innerHTML = '<i class="fas fa-sync-alt mr-2"></i>Cập Nhật Sản Phẩm';
            cancelEditBtn.classList.remove('hidden');

            showMessage(`Đang chỉnh sửa sản phẩm: ${productToEdit.name}`, 'info');
        }

        // NEW: Function to duplicate a product
        function duplicateProduct(productId) {
            const productToDuplicate = products.find(p => p.id === productId);
            if (!productToDuplicate) {
                showMessage('Không tìm thấy sản phẩm để sao chép.', 'error');
                return;
            }

            // Create a deep copy of the product object
            const duplicatedProduct = JSON.parse(JSON.stringify(productToDuplicate));
            
            // Assign a new unique ID
            duplicatedProduct.id = generateProductId();
            // Modify the name to indicate it's a copy
            duplicatedProduct.name = `Bản sao của ${duplicatedProduct.name}`;

            // Reset sold/remaining quantities for the new product
            if (duplicatedProduct.variants) {
                duplicatedProduct.variants.forEach(variant => {
                    variant.sold = 0;
                    variant.remaining = 10; // Default to 10 for new copies, adjust as needed
                });
            } else {
                // If product has no variants, but has a base quantity
                // (Though our current schema uses variants for quantity, good to be safe)
                // duplicatedProduct.sold = 0;
                // duplicatedProduct.remaining = 10;
            }

            // Now, populate the form with the duplicated product's data for editing
            // This effectively switches the form into "add new product" mode but pre-filled
            editProductIdInput.value = ''; // Ensure it's treated as a new product
            newProductNameInput.value = duplicatedProduct.name;
            newProductBasePriceInput.value = duplicatedProduct.basePrice;
            newProductImageInput.value = duplicatedProduct.image;
            newProductDescriptionInput.value = duplicatedProduct.description;

            colorOptionsContainer.innerHTML = '';
            if (duplicatedProduct.options && duplicatedProduct.options.color) {
                duplicatedProduct.options.color.forEach(color => addColorOption(color.name, color.display_image, color.price_impact));
            }

            storageOptionsContainer.innerHTML = '';
            if (duplicatedProduct.options && duplicatedProduct.options.storage) {
                duplicatedProduct.options.storage.forEach(storage => addStorageOption(storage.name, storage.price_impact));
            }

            variantsContainer.innerHTML = '';
            if (duplicatedProduct.variants) {
                duplicatedProduct.variants.forEach(variant => addVariant(
                    variant.color,
                    variant.storage,
                    variant.additional_price_impact,
                    variant.sold,
                    variant.remaining
                ));
            }

            addEditProductTitle.textContent = 'Thêm Mặt Hàng Mới (Sao chép)';
            submitProductBtn.textContent = 'Thêm Sản Phẩm Mới';
            submitProductBtn.classList.remove('bg-blue-600', 'hover:bg-blue-700');
            submitProductBtn.classList.add('bg-green-600', 'hover:bg-green-700');
            submitProductBtn.innerHTML = '<i class="fas fa-save mr-2"></i>Thêm Sản Phẩm Mới';
            cancelEditBtn.classList.remove('hidden'); // Show cancel button

            showMessage(`Đã sao chép sản phẩm "${productToDuplicate.name}". Vui lòng chỉnh sửa và lưu thành sản phẩm mới.`, 'info');
        }


        // Delete product directly from management list
        function deleteProductFromManagement(productId) {
            products = products.filter(p => p.id !== productId);
            localStorage.setItem('products', JSON.stringify(products));
            showMessage(`Sản phẩm ${productId} đã được xóa thành công!`, 'success');
            renderProducts();
            renderManagedProducts();
        }


        // Add Product Form Submission (now handles both add and edit)
        addEditProductForm.addEventListener('submit', (event) => {
            event.preventDefault();

            const productId = editProductIdInput.value;
            const isEditing = !!productId;

            const newProductData = {
                name: newProductNameInput.value.trim(),
                basePrice: parseFloat(newProductBasePriceInput.value),
                image: newProductImageInput.value.trim() || 'https://placehold.co/400x400/CCCCCC/000000?text=No+Image',
                description: newProductDescriptionInput.value.trim(),
                options: {
                    color: [],
                    storage: []
                },
                variants: []
            };

            // Collect color options (names and display_image and price_impact)
            colorOptionsContainer.querySelectorAll('.color-option-group').forEach(group => {
                const nameInput = group.querySelector('.color-name-input');
                const imageInput = group.querySelector('.color-image-input');
                const priceImpactInput = group.querySelector('.color-price-impact-input');
                if (nameInput.value.trim()) {
                    newProductData.options.color.push({
                        name: nameInput.value.trim(),
                        display_image: imageInput.value.trim() || '',
                        price_impact: parseFloat(priceImpactInput.value) || 0
                    });
                }
            });

            // Collect storage options (names and price_impact)
            storageOptionsContainer.querySelectorAll('.storage-option-group').forEach(group => {
                const nameInput = group.querySelector('.storage-option-input');
                const priceImpactInput = group.querySelector('.storage-price-impact-input');
                if (nameInput.value.trim()) {
                    newProductData.options.storage.push({
                        name: nameInput.value.trim(),
                        price_impact: parseFloat(priceImpactInput.value) || 0
                    });
                }
            });

            // Collect variants data
            variantsContainer.querySelectorAll('.variant-group').forEach(group => {
                const colorSelect = group.querySelector('.variant-color-select');
                const storageSelect = group.querySelector('.variant-storage-select');
                const additionalPriceImpactInput = group.querySelector('.variant-additional-price-impact-input');
                const soldInput = group.querySelector('.variant-sold-input');
                const remainingInput = group.querySelector('.variant-remaining-input');

                const variantColor = colorSelect.value || null;
                const variantStorage = storageSelect.value || null;
                const additionalPriceImpact = parseFloat(additionalPriceImpactInput.value) || 0;
                const sold = parseInt(soldInput.value) || 0;
                const remaining = parseInt(remainingInput.value) || 0;

                // Only add variant if at least color or storage is selected
                if (variantColor || variantStorage) {
                    newProductData.variants.push({
                        color: variantColor,
                        storage: variantStorage,
                        additional_price_impact: additionalPriceImpact,
                        sold: sold,
                        remaining: remaining
                    });
                }
            });


            // Basic validation
            if (!newProductData.name || isNaN(newProductData.basePrice) || newProductData.basePrice < 0) {
                showMessage('Vui lòng điền đầy đủ và đúng định dạng các thông tin sản phẩm bắt buộc (Tên, Giá Cơ Bản)!', 'error');
                return;
            }
            if (newProductData.variants.length === 0 && (newProductData.options.color.length > 0 || newProductData.options.storage.length > 0)) {
                showMessage('Vui lòng thêm ít nhất một Biến Thể Sản Phẩm với số lượng và giá tác động cụ thể.', 'error');
                return;
            }

            if (isEditing) {
                const index = products.findIndex(p => p.id === productId);
                if (index > -1) {
                    products[index] = { ...products[index], ...newProductData };
                    showMessage('Sản phẩm đã được cập nhật thành công!', 'success');
                } else {
                    showMessage('Không tìm thấy sản phẩm để cập nhật.', 'error');
                }
            } else {
                newProductData.id = generateProductId();
                products.push(newProductData);
                showMessage('Sản phẩm đã được thêm thành công!', 'success');
            }

            localStorage.setItem('products', JSON.stringify(products));
            renderProducts();
            renderManagedProducts();
            resetAddEditProductForm();
            closeShopManagementModal();
            showProductsBtn.click();
        });

        // Cancel Edit button
        cancelEditBtn.addEventListener('click', resetAddEditProductForm);


        // Helper to add a color option input group (for defining available colors)
        function addColorOption(name = '', display_image = '', price_impact = 0) {
            const group = document.createElement('div');
            group.className = 'color-option-group flex flex-col sm:flex-row gap-2 mb-2 p-2 border rounded-md bg-white items-center';
            group.innerHTML = `
                <input type="text" placeholder="Tên màu (ví dụ: Đỏ)" class="color-name-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 flex-1" value="${name}">
                <div class="flex flex-1 gap-2 w-full sm:w-auto">
                    <input type="url" placeholder="URL Hình ảnh hiển thị" class="color-image-input shadow appearance-none border rounded-l-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 flex-1" value="${display_image}">
                    <button type="button" class="upload-color-image-btn bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-3 rounded-r-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-upload"></i></button>
                </div>
                <input type="number" placeholder="Giá tác động (VND)" class="color-price-impact-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 w-24" value="${price_impact}" min="0">
                <button type="button" class="remove-option-btn bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-3 rounded-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-times"></i></button>
            `;
            group.querySelector('.remove-option-btn').addEventListener('click', () => {
                group.remove();
            });
            group.querySelector('.upload-color-image-btn').addEventListener('click', (e) => {
                const imageUrl = prompt('Nhập URL hình ảnh cho màu sắc (ví dụ: từ GitHub Raw) hoặc chuỗi Base64:\n\nVí dụ URL: https://raw.githubusercontent.com/your-user/your-repo/main/my-color.jpg\nVí dụ Base64: data:image/png;base64,iVBORw0KGgoAAA... (dán chuỗi Base64 dài vào đây)');
                if (imageUrl) {
                    const inputField = e.target.closest('.flex').querySelector('.color-image-input');
                    inputField.value = imageUrl;
                }
            });
            colorOptionsContainer.appendChild(group);
        }

        // Helper to add a storage option input (for defining available storages)
        function addStorageOption(name = '', price_impact = 0) {
            const group = document.createElement('div');
            group.className = 'storage-option-group flex gap-2 mb-2 p-2 border rounded-md bg-white items-center';
            group.innerHTML = `
                <input type="text" placeholder="Dung lượng (ví dụ: 128GB)" class="storage-option-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 flex-1" value="${name}">
                <input type="number" placeholder="Giá tác động (VND)" class="storage-price-impact-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 w-24" value="${price_impact}" min="0">
                <button type="button" class="remove-option-btn bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-3 rounded-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-times"></i></button>
            `;
            group.querySelector('.remove-option-btn').addEventListener('click', () => {
                group.remove();
            });
            storageOptionsContainer.appendChild(group);
        }

        // Helper to add a product variant input group
        function addVariant(color = '', storage = '', additional_price_impact = 0, sold = 0, remaining = 0) {
            // Get current defined colors and storages to populate dropdowns
            const currentColors = Array.from(colorOptionsContainer.querySelectorAll('.color-name-input')).map(input => input.value.trim()).filter(Boolean);
            const currentStorages = Array.from(storageOptionsContainer.querySelectorAll('.storage-option-input')).map(input => input.value.trim()).filter(Boolean);

            const group = document.createElement('div');
            group.className = 'variant-group flex flex-col sm:flex-row gap-2 mb-2 p-2 border rounded-md bg-white items-center';
            group.innerHTML = `
                <select class="variant-color-select shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 flex-1">
                    <option value="">Chọn Màu</option>
                    ${currentColors.map(c => `<option value="${c}" ${c === color ? 'selected' : ''}>${c}</option>`).join('')}
                </select>
                <select class="variant-storage-select shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 flex-1">
                    <option value="">Chọn Dung Lượng</option>
                    ${currentStorages.map(s => `<option value="${s}" ${s === storage ? 'selected' : ''}>${s}</option>`).join('')}
                </select>
                <input type="number" placeholder="Giá tác động riêng" class="variant-additional-price-impact-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 w-24" value="${additional_price_impact}" min="0">
                <input type="number" placeholder="Đã bán" class="variant-sold-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 w-24" value="${sold}" min="0">
                <input type="number" placeholder="Còn lại" class="variant-remaining-input shadow appearance-none border rounded-lg py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-blue-500 w-24" value="${remaining}" min="0">
                <button type="button" class="remove-variant-btn bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-3 rounded-lg transition-all duration-200 text-sm flex-shrink-0"><i class="fas fa-times"></i></button>
            `;
            group.querySelector('.remove-variant-btn').addEventListener('click', () => {
                group.remove();
            });
            variantsContainer.appendChild(group);
        }


        // Add Color Option dynamic inputs
        addColorOptionBtn.addEventListener('click', () => addColorOption());

        // Add Storage Option dynamic inputs
        addStorageOptionBtn.addEventListener('click', () => addStorageOption());

        // Add Variant dynamic inputs
        addVariantBtn.addEventListener('click', () => addVariant());

        // Handle main image upload button click
        uploadMainImageBtn.addEventListener('click', () => {
            const imageUrl = prompt('Nhập URL hình ảnh chính cho sản phẩm (ví dụ: từ GitHub Raw) hoặc chuỗi Base64:\n\nVí dụ URL: https://raw.githubusercontent.com/your-user/your-repo/main/my-product.jpg\nVí dụ Base64: data:image/png;base64,iVBORw0KGgoAAA... (dán chuỗi Base64 dài vào đây)');
            if (imageUrl) {
                newProductImageInput.value = imageUrl;
            }
        });


        // Add Voucher Form Submission
        addVoucherForm.addEventListener('submit', (event) => {
            event.preventDefault();

            const code = newVoucherCodeInput.value.trim().toUpperCase();
            const value = parseFloat(newVoucherValueInput.value);

            if (!code || isNaN(value)) {
                showMessage('Vui lòng điền đầy đủ và đúng định dạng mã voucher và giá trị!', 'error');
                return;
            }

            vouchers[code] = value;
            localStorage.setItem('vouchers', JSON.stringify(vouchers));
            showMessage(`Voucher "${code}" với giá trị ${value} đã được thêm thành công!`, 'success');
            addVoucherForm.reset();
            renderCurrentVouchers(); // Update the list of vouchers
        });

        // Render current vouchers in the management modal
        function renderCurrentVouchers() {
            currentVouchersList.innerHTML = '';
            const voucherKeys = Object.keys(vouchers);
            if (voucherKeys.length === 0) {
                currentVouchersList.innerHTML = `<p class="text-gray-500 italic">Chưa có voucher nào.</p>`;
                return;
            }
            voucherKeys.forEach(code => {
                const value = vouchers[code];
                const displayValue = typeof value === 'number' ? 
                                    (value < 1 ? `${value * 100}%` : formatCurrency(value)) : 
                                    'Miễn phí vận chuyển';
                const voucherItem = document.createElement('div');
                voucherItem.className = 'flex items-center justify-between bg-gray-100 p-3 rounded-lg shadow-sm border border-gray-200';
                voucherItem.innerHTML = `
                    <span class="font-semibold text-gray-700">${code}: <span class="font-normal">${displayValue}</span></span>
                    <button class="delete-voucher-btn bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded-lg text-sm transition-all duration-200" data-voucher-code="${code}"><i class="fas fa-trash-alt"></i></button>
                `;
                voucherItem.querySelector('.delete-voucher-btn').addEventListener('click', (event) => {
                    const codeToDelete = event.target.dataset.voucherCode;
                    delete vouchers[codeToDelete];
                    localStorage.setItem('vouchers', JSON.stringify(vouchers));
                    showMessage(`Voucher "${codeToDelete}" đã được xóa.`, 'info');
                    renderCurrentVouchers();
                });
                currentVouchersList.appendChild(voucherItem);
            });
        }


        // --- Shop Settings Functions ---
        const shopSettingsModal = document.getElementById('shop-settings-modal');
        const closeSettingsModalBtn = document.getElementById('close-settings-modal');
        const shopSettingsForm = document.getElementById('shop-settings-form');
        const shopNameInput = document.getElementById('shop-name-input');
        const backgroundImageURLInput = document.getElementById('background-image-url');
        const uploadBackgroundBtn = document.getElementById('upload-background-btn');
        const bodyElement = document.body;

        function openShopSettingsModal() {
            shopNameInput.value = shopSettings.shopName;
            backgroundImageURLInput.value = shopSettings.backgroundImage;
            toggleModalActive(shopSettingsModal, true);
        }

        function closeShopSettingsModal() {
            toggleModalActive(shopSettingsModal, false);
        }

        function applyShopSettings() {
            pageTitle.textContent = shopSettings.shopName;
            shopNameDisplay.textContent = shopSettings.shopName;
            if (shopSettings.backgroundImage) {
                bodyElement.style.backgroundImage = `url('${shopSettings.backgroundImage}')`;
            } else {
                bodyElement.style.backgroundImage = 'none';
                bodyElement.style.backgroundColor = '#fdf8f4'; // Màu nền mặc định ấm dịu
            }
            // Update shop address display in header
            shopAddressDisplay.textContent = shopSettings.shopAddress || 'Chưa cập nhật';
            if (shopSettings.shopAddress) {
                openAddressInMapBtn.classList.remove('hidden');
            } else {
                openAddressInMapBtn.classList.add('hidden');
            }
        }

        shopSettingsForm.addEventListener('submit', (event) => {
            event.preventDefault();
            shopSettings.shopName = shopNameInput.value.trim();
            shopSettings.backgroundImage = backgroundImageURLInput.value.trim();
            shopSettings.shopAddress = shopAddressSettingsInput.value.trim(); // Ensure this is also saved
            localStorage.setItem('shopSettings', JSON.stringify(shopSettings));
            applyShopSettings();
            showMessage('Cài đặt cửa hàng đã được lưu thành công!', 'success');
            closeShopSettingsModal();
        });

        uploadBackgroundBtn.addEventListener('click', () => {
            const imageUrl = prompt('Nhập URL hình nền mới (ví dụ: từ GitHub Raw) hoặc chuỗi Base64:\n\nVí dụ URL: https://raw.githubusercontent.com/your-user/your-repo/main/my-background.jpg\nVí dụ Base64: data:image/png;base64,iVBORw0KGgoAAA... (dán chuỗi Base64 dài vào đây)');
            if (imageUrl) {
                backgroundImageURLInput.value = imageUrl;
            }
        });

        // --- Shop Analytics & Address Management Functions (NEW) ---
        function openShopAnalyticsModal() {
            toggleModalActive(shopAnalyticsModal, true);
            // Populate shop address in settings input
            shopAddressSettingsInput.value = shopSettings.shopAddress || '';
            // Initially display analytics (or clear for new calculation)
            updateAnalyticsDisplay();
        }

        function closeShopAnalyticsModal() {
            toggleModalActive(shopAnalyticsModal, false);
        }

        viewAnalyticsReportBtn.addEventListener('click', () => {
            updateAnalyticsDisplay();
        });

        function updateAnalyticsDisplay() {
            const startDate = reportStartDateInput.value ? new Date(reportStartDateInput.value).setHours(0,0,0,0) : 0; // Start of time
            const endDate = reportEndDateInput.value ? new Date(reportEndDateInput.value).setHours(23,59,59,999) : Date.now(); // End of today

            let totalRevenue = 0;
            let totalSalesQuantity = 0;
            let totalInboundQuantity = 0; // Sum of all remaining products initially

            products.forEach(product => {
                totalInboundQuantity += product.variants ? product.variants.reduce((sum, variant) => sum + (variant.remaining || 0) + (variant.sold || 0), 0) : 0;
            });

            orders.forEach(order => {
                const orderDate = new Date(order.createdAt).getTime();
                if (orderDate >= startDate && orderDate <= endDate) {
                    totalRevenue += order.product.priceAtOrder;
                    totalSalesQuantity++; // Assuming 1 product per order for simplicity
                }
            });

            revenueDisplay.textContent = formatCurrency(totalRevenue);
            salesQuantityDisplay.textContent = totalSalesQuantity.toString();
            inboundQuantityDisplay.textContent = totalInboundQuantity.toString();
        }

        saveShopAddressBtn.addEventListener('click', () => {
            const newAddress = shopAddressSettingsInput.value.trim();
            shopSettings.shopAddress = newAddress;
            localStorage.setItem('shopSettings', JSON.stringify(shopSettings));
            applyShopSettings(); // Update display in header
            showMessage('Địa chỉ cửa hàng đã được lưu thành công!', 'success');
        });

        openGoogleMapsBtn.addEventListener('click', () => {
            const address = shopAddressSettingsInput.value.trim();
            const mapUrl = address ? `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(address)}` : `https://www.google.com/maps/`;
            window.open(mapUrl, '_blank');
        });

        openAddressInMapBtn.addEventListener('click', () => {
            const address = shopSettings.shopAddress;
            if (address) {
                const mapUrl = `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(address)}`;
                window.open(mapUrl, '_blank');
            } else {
                showMessage('Chưa có địa chỉ cửa hàng để hiển thị trên bản đồ.', 'info');
            }
        });


        // --- Sensitive Data Masking Functions ---
        document.querySelectorAll('.toggle-visibility-btn').forEach(button => {
            button.addEventListener('click', (event) => {
                const targetId = button.dataset.targetId;
                const inputElement = document.getElementById(targetId);
                const icon = button.querySelector('i');

                if (targetId === 'customer-phone') {
                    // Đối với số điện thoại: chuyển đổi giữa type 'tel' và 'password'
                    if (inputElement.type === 'tel') { // Hiện tại đang hiển thị, chuyển sang che
                        inputElement.type = 'password';
                        icon.classList.remove('fa-eye');
                        icon.classList.add('fa-eye-slash');
                    } else { // Hiện tại đang che, chuyển sang hiển thị
                        inputElement.type = 'tel';
                        icon.classList.remove('fa-eye-slash');
                        icon.classList.add('fa-eye');
                    }
                } else if (targetId === 'customer-address') {
                    // Đối với địa chỉ (textarea): chuyển đổi lớp CSS để làm mờ nội dung
                    if (inputElement.classList.contains('blurred-content')) { // Hiện tại đang mờ, chuyển sang rõ
                        inputElement.classList.remove('blurred-content');
                        icon.classList.remove('fa-eye-slash');
                        icon.classList.add('fa-eye');
                    } else { // Hiện tại đang rõ, chuyển sang mờ
                        inputElement.classList.add('blurred-content');
                        icon.classList.remove('fa-eye');
                        icon.classList.add('fa-eye-slash');
                    }
                }
            });
        });


        // --- Event Listeners ---
        closeProductModalBtn.addEventListener('click', closeProductDetailModal);
        productDetailModal.addEventListener('click', (e) => {
            if (e.target === productDetailModal) {
                closeProductDetailModal();
            }
        });

        closeOrderModalBtn.addEventListener('click', closeOrderCreationModal);
        orderCreationModal.addEventListener('click', (e) => {
            if (e.target === orderCreationModal) {
                closeOrderCreationModal();
            }
        });

        closeManagementModalBtn.addEventListener('click', closeShopManagementModal);
        shopManagementModal.addEventListener('click', (e) => {
            if (e.target === shopManagementModal) {
                closeShopManagementModal();
            }
        });

        closeSettingsModalBtn.addEventListener('click', closeShopSettingsModal);
        shopSettingsModal.addEventListener('click', (e) => {
            if (e.target === shopSettingsModal) {
                closeShopSettingsModal();
            }
        });

        closeAnalyticsModal.addEventListener('click', closeShopAnalyticsModal);
        shopAnalyticsModal.addEventListener('click', (e) => {
            if (e.target === shopAnalyticsModal) {
                closeShopAnalyticsModal();
            }
        });


        // Điều hướng giữa các tab và cập nhật danh sách đơn hàng/sản phẩm
        showProductsBtn.addEventListener('click', () => showSection('product-list-section'));
        showCreatedOrdersBtn.addEventListener('click', () => { updateOrderLists(); showSection('created-orders-section'); });
        showShippingOrdersBtn.addEventListener('click', () => { updateOrderLists(); showSection('shipping-orders-section'); });
        showDeliveredOrdersBtn.addEventListener('click', () => { updateOrderLists(); showSection('delivered-orders-section'); });
        openManagementModalBtn.addEventListener('click', openShopManagementModal);
        openSettingsModalBtn.addEventListener('click', openShopSettingsModal);
        openShopAnalyticsModalBtn.addEventListener('click', openShopAnalyticsModal);

        // Search event listeners for order lists
        searchCreatedOrdersInput.addEventListener('input', () => renderOrderList(createdOrdersList, 'created', searchCreatedOrdersInput.value));
        clearSearchCreatedOrdersBtn.addEventListener('click', () => {
            searchCreatedOrdersInput.value = '';
            renderOrderList(createdOrdersList, 'created');
        });

        searchShippingOrdersInput.addEventListener('input', () => renderOrderList(shippingOrdersList, 'shipping', searchShippingOrdersInput.value));
        clearSearchShippingOrdersBtn.addEventListener('click', () => {
            searchShippingOrdersInput.value = '';
            renderOrderList(shippingOrdersList, 'shipping');
        });

        searchDeliveredOrdersInput.addEventListener('input', () => renderOrderList(deliveredOrdersList, 'delivered', searchDeliveredOrdersInput.value));
        clearSearchDeliveredOrdersBtn.addEventListener('click', () => {
            searchDeliveredOrdersInput.value = '';
            renderOrderList(deliveredOrdersList, 'delivered');
        });


        // Khởi tạo ứng dụng
        document.addEventListener('DOMContentLoaded', () => {
            applyShopSettings(); // Apply saved settings on load
            renderProducts();
            updateOrderLists();
            showSection('product-list-section');
        });
    </script>
</body>
</html>
