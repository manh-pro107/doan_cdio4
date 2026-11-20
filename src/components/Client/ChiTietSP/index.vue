
<template>
  <main class="detail-page">
    <!-- LOADING -->
    <div v-if="loading" class="page-state">
      <div class="loading-spinner"></div>
      <p>Đang tải thông tin sản phẩm...</p>
    </div>

    <!-- ERROR -->
    <div v-else-if="pageError" class="container page-state">
      <div class="state-icon">
        <i class="fa-solid fa-box-open"></i>
      </div>
      <h2>Không tải được sản phẩm</h2>
      <p>{{ pageError }}</p>
      <button class="btn-primary-custom" @click="loadProductDetails">
        Thử lại
      </button>
      <button class="btn-light-custom" @click="$router.push('/')">
        Về trang chủ
      </button>
    </div>

    <!-- PRODUCT DETAIL -->
    <template v-else-if="product">
      <div class="container detail-container">
        <!-- BREADCRUMB -->
        <nav class="breadcrumb-custom">
          <RouterLink to="/">Trang chủ</RouterLink>
          <i class="fa-solid fa-chevron-right"></i>
          <span>Sản phẩm</span>
          <i class="fa-solid fa-chevron-right"></i>
          <span class="breadcrumb-current">
            {{ product.TenSP }}
          </span>
        </nav>

        <!-- MAIN PRODUCT -->
        <section class="product-panel">
          <div class="product-layout">
            <!-- GALLERY -->
            <div class="gallery-column">
              <div class="gallery-main">
                <span class="gallery-label">
                  <i class="fa-solid fa-cube"></i>
                  TECHSHOP PRODUCT
                </span>

                <span
                  v-if="product.TrangThaiSP === 'NgungBan'"
                  class="product-status status-off"
                >
                  Ngừng kinh doanh
                </span>
                <span
                  v-else-if="availableStock <= 0"
                  class="product-status status-out"
                >
                  Hết hàng
                </span>
                <span v-else class="product-status status-active">
                  Đang bán
                </span>

                <img
                  v-if="galleryImages.length"
                  :src="selectedImage || galleryImages[0]"
                  :alt="product.TenSP"
                  @error="handleImageError"
                />

                <div v-else class="no-image">
                  <i class="fa-regular fa-image"></i>
                  <span>Chưa có hình ảnh</span>
                </div>

                <div class="image-caption">
                  <span>Hình ảnh sản phẩm</span>
                  <span>{{ galleryImages.length }} ảnh</span>
                </div>
              </div>

              <div v-if="galleryImages.length > 1" class="gallery-thumbnails">
                <button
                  v-for="(img, index) in galleryImages"
                  :key="img + index"
                  type="button"
                  class="thumbnail"
                  :class="{ active: (selectedImage || galleryImages[0]) === img }"
                  @click="changeImage(img)"
                >
                  <img :src="img" :alt="`${product.TenSP} ${index + 1}`" />
                </button>
              </div>

              <div class="trust-row">
                <div>
                  <i class="fa-solid fa-shield-halved"></i>
                  <span>Thông tin từ cửa hàng</span>
                </div>
                <div>
                  <i class="fa-solid fa-box"></i>
                  <span>Tồn kho được cập nhật</span>
                </div>
              </div>
            </div>

            <!-- PRODUCT INFORMATION -->
            <div class="product-info-column">
              <div class="product-eyebrow">
                <span class="eyebrow-dot"></span>
                TECHSHOP COMPUTER STORE
              </div>

              <h1 class="product-title">
                {{ product.TenSP }}
              </h1>

              <div class="product-meta">
                <span>Mã sản phẩm: <b>#{{ product.MaSP }}</b></span>
                <span class="meta-divider"></span>
                <span>
                  <i class="fa-solid fa-star"></i>
                  {{ averageRating.toFixed(1) }}/5
                  <small>({{ reviews.length }} đánh giá)</small>
                </span>
              </div>

              <div class="price-card">
                <span class="price-label">Giá bán tại cửa hàng</span>
                <div class="price-value">
                  {{ formatMoney(product.GiaBan) }}
                  <span>₫</span>
                </div>
                <p class="price-note">
                  Giá lấy từ dữ liệu sản phẩm hiện tại.
                </p>
              </div>

              <div class="stock-card">
                <div class="stock-icon">
                  <i
                    :class="
                      availableStock > 0
                        ? 'fa-solid fa-check'
                        : 'fa-solid fa-box-open'
                    "
                  ></i>
                </div>

                <div class="stock-copy">
                  <strong>
                    {{ availableStock > 0 ? "Sẵn sàng phục vụ" : "Tạm hết hàng" }}
                  </strong>
                  <span>
                    {{
                      availableStock > 0
                        ? `Còn ${availableStock} sản phẩm có thể bán`
                        : "Hiện chưa có sản phẩm có thể bán"
                    }}
                  </span>
                </div>
              </div>

              <div class="detail-description">
                <h3>Mô tả sản phẩm</h3>
                <p>
                  {{
                    detail?.MoTaChiTiet ||
                    "Sản phẩm chưa có mô tả chi tiết."
                  }}
                </p>
              </div>

              <!-- QUANTITY -->
              <div class="quantity-section">
                <label>Số lượng</label>

                <div class="quantity-controls">
                  <button
                    type="button"
                    aria-label="Giảm số lượng"
                    :disabled="quantity <= 1"
                    @click="quantity = Math.max(1, quantity - 1)"
                  >
                    <i class="fa-solid fa-minus"></i>
                  </button>

                  <input
                    v-model.number="quantity"
                    type="number"
                    min="1"
                    :max="availableStock"
                    :disabled="availableStock <= 0"
                    @change="normalizeQuantity"
                  />

                  <button
                    type="button"
                    aria-label="Tăng số lượng"
                    :disabled="quantity >= availableStock"
                    @click="quantity = Math.min(availableStock, quantity + 1)"
                  >
                    <i class="fa-solid fa-plus"></i>
                  </button>
                </div>

                <span class="quantity-hint">
                  Tối đa {{ availableStock }} sản phẩm có thể bán
                </span>
              </div>

              <!-- ACTIONS -->
              <div class="product-actions">
                <button
                  type="button"
                  class="btn-buy"
                  :disabled="!canBuy || addingToCart"
                  @click="buyNow"
                >
                  <i class="fa-solid fa-bolt"></i>
                  <span>{{ addingToCart ? "Đang xử lý..." : "MUA NGAY" }}</span>
                </button>

                <button
                  type="button"
                  class="btn-add-cart"
                  :disabled="!canBuy || addingToCart"
                  @click="addToCart"
                >
                  <i class="fa-solid fa-cart-plus"></i>
                  <span>THÊM VÀO GIỎ</span>
                </button>
              </div>

              <p v-if="product.TrangThaiSP === 'NgungBan'" class="action-warning">
                Sản phẩm đã ngừng kinh doanh và không thể đặt mua.
              </p>

              <div class="service-grid">
                <div class="service-item">
                  <div class="service-icon">
                    <i class="fa-solid fa-truck-fast"></i>
                  </div>
                  <div>
                    <strong>Giao hàng</strong>
                    <span>Thông tin theo chính sách shop</span>
                  </div>
                </div>

                <div class="service-item">
                  <div class="service-icon">
                    <i class="fa-solid fa-headset"></i>
                  </div>
                  <div>
                    <strong>Hỗ trợ</strong>
                    <span>Tư vấn sản phẩm</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </section>

        <!-- SPECIFICATIONS -->
        <section class="content-card specifications-section">
          <div class="section-heading">
            <div>
              <span class="section-kicker">PRODUCT INFORMATION</span>
              <h2>Thông số kỹ thuật</h2>
              <p>Thông tin được lấy từ dữ liệu chi tiết sản phẩm.</p>
            </div>
            <div class="section-heading-icon">
              <i class="fa-solid fa-microchip"></i>
            </div>
          </div>

          <div v-if="specEntries.length" class="spec-grid">
            <div
              v-for="([key, value], index) in specEntries"
              :key="key"
              class="spec-item"
            >
              <span class="spec-index">
                {{ String(index + 1).padStart(2, "0") }}
              </span>
              <span class="spec-name">{{ formatSpecName(key) }}</span>
              <strong class="spec-value">{{ value }}</strong>
            </div>
          </div>

          <div v-else class="empty-state">
            <i class="fa-solid fa-circle-info"></i>
            <p>Sản phẩm chưa có thông số kỹ thuật.</p>
          </div>
        </section>

        <!-- RELATED PRODUCTS -->
        <section class="content-card related-section">
          <div class="section-heading">
            <div>
              <span class="section-kicker">YOU MAY ALSO LIKE</span>
              <h2>Sản phẩm liên quan</h2>
              <p>Khám phá thêm các sản phẩm khác tại TechShop.</p>
            </div>

            <RouterLink to="/" class="view-more-link">
              Xem tất cả <i class="fa-solid fa-arrow-right"></i>
            </RouterLink>
          </div>

          <div v-if="relatedProducts.length" class="related-grid">
            <button
              v-for="item in relatedProducts"
              :key="item.MaSP"
              type="button"
              class="related-card"
              @click="openProduct(item)"
            >
              <div class="related-image">
                <img
                  :src="item.HinhAnh"
                  :alt="item.TenSP"
                  @error="handleRelatedImageError"
                />
                <span
                  v-if="Number(item.SoLuongTon || 0) <= 0"
                  class="related-stock"
                >
                  Hết hàng
                </span>
              </div>

              <div class="related-content">
                <span class="related-code">MÃ #{{ item.MaSP }}</span>
                <h3>{{ item.TenSP }}</h3>
                <strong class="related-price">
                  {{ formatMoney(item.GiaBan) }}₫
                </strong>
                <span class="related-link">
                  Xem sản phẩm <i class="fa-solid fa-arrow-right"></i>
                </span>
              </div>
            </button>
          </div>

          <div v-else class="empty-state">
            Chưa có sản phẩm liên quan để hiển thị.
          </div>
        </section>

        <!-- RATINGS AND COMMENTS -->
        <section class="content-card reviews-section">
          <div class="section-heading">
            <div>
              <span class="section-kicker">CUSTOMER FEEDBACK</span>
              <h2>Đánh giá & nhận xét</h2>
              <p>Chia sẻ trải nghiệm thực tế của bạn về sản phẩm.</p>
            </div>
            <div class="section-heading-icon rating-heading-icon">
              <i class="fa-solid fa-star"></i>
            </div>
          </div>

          <!-- RATING SUMMARY -->
          <div class="rating-summary">
            <div class="rating-overall">
              <strong class="rating-number">
                {{ averageRating.toFixed(1) }}
                <span>/ 5</span>
              </strong>

              <div class="stars-row">
                <i
                  v-for="star in 5"
                  :key="star"
                  class="fa-solid fa-star"
                  :class="{ 'star-muted': star > Math.round(averageRating) }"
                ></i>
              </div>

              <span class="rating-count">
                {{ reviews.length }} lượt đánh giá
              </span>
            </div>

            <div class="rating-breakdown">
              <div
                v-for="star in [5, 4, 3, 2, 1]"
                :key="star"
                class="rating-bar-row"
              >
                <span>{{ star }} sao</span>

                <div class="rating-track">
                  <div
                    class="rating-fill"
                    :style="{ width: ratingPercent(star) + '%' }"
                  ></div>
                </div>

                <span class="rating-percent">
                  {{ ratingPercent(star) }}%
                </span>
              </div>
            </div>
          </div>

          <!-- WRITE REVIEW -->
          <div class="review-form-card">
            <div class="review-form-heading">
              <div class="review-form-icon">
                <i class="fa-regular fa-pen-to-square"></i>
              </div>
              <div>
                <h3>Viết nhận xét của bạn</h3>
                <p>Đánh giá dựa trên trải nghiệm thực tế.</p>
              </div>
            </div>

            <div class="review-star-picker">
              <span>Chọn số sao:</span>
              <div class="star-buttons">
                <button
                  v-for="star in 5"
                  :key="star"
                  type="button"
                  :aria-label="`${star} sao`"
                  @click="reviewForm.SoSao = star"
                >
                  <i
                    class="fa-solid fa-star"
                    :class="{ selected: star <= reviewForm.SoSao }"
                  ></i>
                </button>
              </div>
              <strong>{{ reviewForm.SoSao }}/5</strong>
            </div>

            <textarea
              v-model.trim="reviewForm.NoiDung"
              class="review-textarea"
              rows="4"
              maxlength="2000"
              placeholder="Bạn cảm nhận thế nào về sản phẩm này?"
            ></textarea>

            <div class="review-form-footer">
              <span>{{ reviewForm.NoiDung.length }}/2000 ký tự</span>
              <button
                type="button"
                class="btn-submit-review"
                :disabled="submittingReview || !reviewForm.NoiDung.trim()"
                @click="submitReview"
              >
                <i class="fa-solid fa-paper-plane"></i>
                {{ submittingReview ? "Đang gửi..." : "Gửi đánh giá" }}
              </button>
            </div>

            <p v-if="!isLoggedIn" class="login-review-note">
              Bạn cần đăng nhập để gửi đánh giá.
            </p>
          </div>

          <!-- REVIEW LIST -->
          <div class="review-list-heading">
            <h3>Nhận xét từ khách hàng</h3>
            <span>{{ reviews.length }} đánh giá</span>
          </div>

          <div v-if="loadingReviews" class="review-loading">
            <div class="loading-spinner small-spinner"></div>
            Đang tải nhận xét...
          </div>

          <div v-else-if="reviews.length" class="review-list">
            <article
              v-for="(review, index) in reviews"
              :key="review.MaDG || index"
              class="review-card"
            >
              <div class="review-avatar">
                {{ getReviewAuthor(review).charAt(0).toUpperCase() }}
              </div>

              <div class="review-body">
                <div class="review-topline">
                  <div>
                    <strong class="review-author">
                      {{ getReviewAuthor(review) }}
                    </strong>
                    <div class="review-stars">
                      <i
                        v-for="star in 5"
                        :key="star"
                        class="fa-solid fa-star"
                        :class="{ 'star-muted': star > Number(review.SoSao || 0) }"
                      ></i>
                    </div>
                  </div>

                  <time v-if="review.NgayDG" class="review-date">
                    {{ formatDate(review.NgayDG) }}
                  </time>
                </div>

                <p class="review-content">
                  {{ review.NoiDung }}
                </p>
              </div>
            </article>
          </div>

          <div v-else class="empty-reviews">
            <div class="empty-review-icon">
              <i class="fa-regular fa-comments"></i>
            </div>
            <h3>Chưa có đánh giá nào</h3>
            <p>Hãy là người đầu tiên chia sẻ nhận xét về sản phẩm này.</p>
          </div>

          <p v-if="reviewError" class="review-error">
            {{ reviewError }}
          </p>
        </section>
      </div>
    </template>
  </main>
</template>

<script>
import axios from "axios";

const API = "http://127.0.0.1:8000/api";

export default {
  name: "ChiTietSanPham",

  data() {
    return {
      loading: true,
      pageError: "",
      product: null,
      detail: null,
      products: [],
      reviews: [],
      loadingReviews: false,
      selectedImage: "",
      quantity: 1,
      addingToCart: false,
      submittingReview: false,
      reviewError: "",
      reviewForm: {
        SoSao: 5,
        NoiDung: "",
      },
    };
  },

  computed: {
    availableStock() {
      if (!this.product) return 0;

      const stockRelation =
        this.product.ton_kho || this.product.tonKho || {};

      const total = Number(
        stockRelation.SoLuongTon ?? this.product.SoLuongTon ?? 0
      );

      const reserved = Number(
        stockRelation.SoLuongDangGiu ??
          this.product.SoLuongDangGiu ??
          0
      );

      if (this.product.TrangThaiSP === "NgungBan") return 0;

      return Math.max(0, total - reserved);
    },

    canBuy() {
      return (
        this.product &&
        this.product.TrangThaiSP !== "NgungBan" &&
        this.availableStock > 0 &&
        this.quantity >= 1 &&
        this.quantity <= this.availableStock
      );
    },

    galleryImages() {
      const result = [];

      if (this.product?.HinhAnh) {
        result.push(String(this.product.HinhAnh));
      }

      let detailImages = this.detail?.HinhAnhChiTiet || [];

      if (typeof detailImages === "string") {
        try {
          detailImages = JSON.parse(detailImages);
        } catch {
          detailImages = [];
        }
      }

      if (Array.isArray(detailImages)) {
        detailImages.forEach((image) => {
          if (!image) return;

          let src = String(image).trim();

          if (
            src &&
            !/^https?:\/\//i.test(src) &&
            !src.startsWith("/")
          ) {
            src = `/img_chitiet/${src}`;
          }

          if (src) result.push(src);
        });
      }

      return [...new Set(result)];
    },

    specEntries() {
      let specs = this.detail?.ThongSo || {};

      if (typeof specs === "string") {
        try {
          specs = JSON.parse(specs);
        } catch {
          specs = {};
        }
      }

      if (!specs || typeof specs !== "object" || Array.isArray(specs)) {
        return [];
      }

      return Object.entries(specs).filter(
        ([, value]) => value !== null && value !== ""
      );
    },

    averageRating() {
      if (!this.reviews.length) return 0;

      const total = this.reviews.reduce(
        (sum, review) => sum + Number(review.SoSao || 0),
        0
      );

      return total / this.reviews.length;
    },

    relatedProducts() {
      if (!this.product) return [];

      return this.products
        .filter(
          (item) =>
            Number(item.MaSP) !== Number(this.product.MaSP) &&
            item.TrangThaiSP !== "NgungBan"
        )
        .slice(0, 5);
    },

    isLoggedIn() {
      return Boolean(
        localStorage.getItem("token") &&
        localStorage.getItem("isLogin") === "true"
      );
    },
  },

  watch: {
    "$route.query.MaSP"() {
      this.loadProductDetails();
    },

    "$route.params.MaSP"() {
      this.loadProductDetails();
    },

    "$route.params.id"() {
      this.loadProductDetails();
    },
  },

  mounted() {
    this.loadProductDetails();
  },

  methods: {
    getProductId() {
      return (
        this.$route.query.MaSP ||
        this.$route.params.MaSP ||
        this.$route.params.id ||
        localStorage.getItem("selectedMaSP")
      );
    },

    async loadProductDetails() {
      const productId = this.getProductId();

      this.loading = true;
      this.pageError = "";
      this.product = null;
      this.detail = null;
      this.selectedImage = "";
      this.quantity = 1;
      this.reviews = [];

      if (!productId) {
        this.pageError = "Chưa xác định được sản phẩm cần xem.";
        this.loading = false;
        return;
      }

      try {
        const [productsResponse, detailResponse] = await Promise.all([
          axios.get(`${API}/san-pham/get-data`),
          axios.get(
            `${API}/chi-tiet-san-pham/get-detail/${encodeURIComponent(productId)}`
          ),
        ]);

        const allProducts = productsResponse?.data?.data;

        if (
          productsResponse?.data?.status !== true ||
          !Array.isArray(allProducts)
        ) {
          throw new Error("Không lấy được danh sách sản phẩm.");
        }

        this.products = allProducts;

        this.product = allProducts.find(
          (item) => Number(item.MaSP) === Number(productId)
        );

        if (!this.product) {
          this.pageError = "Sản phẩm không tồn tại hoặc không còn được hiển thị.";
          return;
        }

        if (
          detailResponse?.data?.status === true &&
          detailResponse?.data?.data
        ) {
          this.detail = detailResponse.data.data;
        }

        this.selectedImage = this.galleryImages[0] || "";

        await this.loadReviews(this.product.MaSP);
      } catch (error) {
        console.error("Lỗi tải chi tiết sản phẩm:", error);

        this.pageError =
          error.response?.data?.message ||
          error.message ||
          "Không thể tải dữ liệu sản phẩm.";
      } finally {
        this.loading = false;
      }
    },

    async loadReviews(productId) {
      this.loadingReviews = true;
      this.reviewError = "";

      try {
        const response = await axios.get(
          `${API}/danh-gia/get-data/${encodeURIComponent(productId)}`
        );

        if (response?.data?.status === true) {
          const data = response.data.data;

          this.reviews = Array.isArray(data)
            ? data
            : Array.isArray(data?.data)
              ? data.data
              : [];
        } else {
          this.reviews = [];
          this.reviewError =
            response?.data?.message || "Không tải được đánh giá.";
        }
      } catch (error) {
        console.error("Lỗi tải đánh giá:", error);
        this.reviews = [];
        this.reviewError = "Chưa tải được đánh giá từ máy chủ.";
      } finally {
        this.loadingReviews = false;
      }
    },

    changeImage(image) {
      this.selectedImage = image;
    },

    handleImageError(event) {
      event.target.style.visibility = "hidden";
    },

    handleRelatedImageError(event) {
      event.target.style.visibility = "hidden";
    },

    normalizeQuantity() {
      const input = Number(this.quantity);

      if (!Number.isFinite(input) || input < 1) {
        this.quantity = 1;
      } else {
        this.quantity = Math.min(
          Math.floor(input),
          Math.max(1, this.availableStock)
        );
      }
    },

    formatMoney(value) {
      return Number(value || 0).toLocaleString("vi-VN");
    },

    formatSpecName(key) {
      const labels = {
        CPU: "Bộ vi xử lý",
        RAM: "Bộ nhớ RAM",
        VGA: "Card đồ họa",
        O_Cung: "Ổ cứng",
        ManHinh: "Màn hình",
        KichThuoc: "Kích thước",
        DoPhanGiai: "Độ phân giải",
        TanSoQuet: "Tần số quét",
        TamNen: "Tấm nền",
        ThoiGianDapUng: "Thời gian đáp ứng",
        Loai: "Loại",
        KetNoi: "Kết nối",
        DenNen: "Đèn nền",
        DPI: "Độ phân giải chuột",
        SoNut: "Số nút",
        TrongLuong: "Trọng lượng",
        DungLuong: "Dung lượng",
        Chuan: "Chuẩn",
        GiaoTiep: "Giao tiếp",
        TocDoDoc: "Tốc độ đọc",
        TocDoGhi: "Tốc độ ghi",
      };

      if (labels[key]) return labels[key];

      return String(key)
        .replace(/_/g, " ")
        .replace(/([a-z])([A-Z])/g, "$1 $2")
        .replace(/^./, (character) => character.toUpperCase());
    },

    ratingPercent(star) {
      if (!this.reviews.length) return 0;

      const count = this.reviews.filter(
        (review) => Number(review.SoSao) === star
      ).length;

      return Math.round((count / this.reviews.length) * 100);
    },

    getReviewAuthor(review) {
      return (
        review?.nguoi_dung?.HoTen ||
        review?.nguoiDung?.HoTen ||
        review?.user?.HoTen ||
        review?.HoTen ||
        review?.TenND ||
        review?.Email ||
        (review?.MaND ? `Khách hàng #${review.MaND}` : "Khách hàng")
      );
    },

    formatDate(value) {
      if (!value) return "";

      const date = new Date(value);

      if (Number.isNaN(date.getTime())) return "";

      return date.toLocaleDateString("vi-VN");
    },

    notify(type, message) {
      if (this.$toast && typeof this.$toast[type] === "function") {
        this.$toast[type](message);
      } else {
        window.alert(message);
      }
    },

    async addToCart() {
      if (!this.canBuy || this.addingToCart) return false;

      const token = localStorage.getItem("token");

      if (!token || localStorage.getItem("isLogin") !== "true") {
        this.notify("info", "Bạn cần đăng nhập để thêm sản phẩm vào giỏ hàng.");
        this.$router.push("/dang-nhap");
        return false;
      }

      this.addingToCart = true;

      try {
        const response = await axios.post(
          `${API}/gio-hang/add-data`,
          {
            MaSP: this.product.MaSP,
            SoLuong: this.quantity,
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
            timeout: 15000,
          }
        );

        if (response?.data?.status !== true) {
          this.notify(
            "error",
            response?.data?.message || "Không thể thêm sản phẩm vào giỏ hàng."
          );
          return false;
        }

        window.dispatchEvent(new Event("storage"));
        this.notify("success", "Đã thêm sản phẩm vào giỏ hàng.");
        return true;
      } catch (error) {
        console.error("Lỗi thêm sản phẩm vào giỏ:", error);

        this.notify(
          "error",
          error.response?.data?.message ||
            error.response?.data?.errors?.SoLuong?.[0] ||
            "Không thể thêm sản phẩm vào giỏ hàng."
        );

        return false;
      } finally {
        this.addingToCart = false;
      }
    },

    // MUA NGAY: chuyển thẳng sang trang đặt hàng
// Không thêm sản phẩm vào giỏ hàng
async buyNow() {
  if (!this.canBuy) {
    this.notify("error", "Sản phẩm hiện không thể đặt mua.");
    return;
  }

  const token = localStorage.getItem("token");

  if (!token || localStorage.getItem("isLogin") !== "true") {
    this.notify("info", "Bạn cần đăng nhập để đặt mua sản phẩm.");
    this.$router.push("/dang-nhap");
    return;
  }

  // Gửi đúng mã sản phẩm và số lượng khách đang chọn
  this.$router.push({
    path: "/thanh-toantoan",
    query: {
      mode: "single",
      MaSP: String(this.product.MaSP),
      SoLuong: String(this.quantity),
    },
  });
},

    async submitReview() {
      if (!this.product || this.submittingReview) return;

      const token = localStorage.getItem("token");

      if (!token || localStorage.getItem("isLogin") !== "true") {
        this.notify("info", "Bạn cần đăng nhập để gửi đánh giá.");
        this.$router.push("/dang-nhap");
        return;
      }

      if (!this.reviewForm.NoiDung.trim()) {
        this.notify("error", "Vui lòng nhập nội dung nhận xét.");
        return;
      }

      this.submittingReview = true;
      this.reviewError = "";

      try {
        const response = await axios.post(
          `${API}/danh-gia/add-data`,
          {
            MaSP: this.product.MaSP,
            SoSao: this.reviewForm.SoSao,
            NoiDung: this.reviewForm.NoiDung.trim(),
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
            timeout: 15000,
          }
        );

        if (response?.data?.status !== true) {
          this.notify(
            "error",
            response?.data?.message || "Không thể gửi đánh giá."
          );
          return;
        }

        this.notify("success", "Đã gửi đánh giá.");
        this.reviewForm = {
          SoSao: 5,
          NoiDung: "",
        };

        await this.loadReviews(this.product.MaSP);
      } catch (error) {
        console.error("Lỗi gửi đánh giá:", error);

        this.reviewError =
          error.response?.data?.message ||
          error.response?.data?.errors?.NoiDung?.[0] ||
          "Không thể gửi đánh giá. Vui lòng kiểm tra lại.";
        this.notify("error", this.reviewError);
      } finally {
        this.submittingReview = false;
      }
    },

    openProduct(item) {
      this.$router.push({
        path: "/chi-tiet-sp",
        query: {
          MaSP: item.MaSP,
        },
      });
    },
  },
};
</script>

<style scoped>
/* =========================================================
   BIẾN MÀU - đổi ở đây là đổi cả trang
   (muốn quay lại tông đỏ: --primary:#e53945; --grad-primary:linear-gradient(135deg,#ef515c,#d42f40))
   ========================================================= */
.detail-page {
  --ink: #0f172a;
  --text: #334155;
  --muted: #64748b;
  --line: #e2e8f0;
  --soft: #f1f5f9;
  --primary: #4f46e5;
  --primary-dark: #3730a3;
  --accent: #06b6d4;
  --hot: #f43f5e;
  --gold: #f59e0b;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --grad-hot: linear-gradient(135deg, #fb7185 0%, #f43f5e 50%, #f97316 100%);
  --grad-success: linear-gradient(135deg, #34d399 0%, #059669 100%);
  --shadow-sm: 0 2px 10px rgba(15, 23, 42, 0.06);
  --shadow-md: 0 10px 30px rgba(15, 23, 42, 0.08);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  position: relative;
  min-height: 100vh;
  padding: 22px 0 70px;
  color: var(--ink);
  background:
    radial-gradient(900px 480px at 100% -5%, rgba(99, 102, 241, 0.14), transparent 60%),
    radial-gradient(700px 480px at -5% 25%, rgba(6, 182, 212, 0.1), transparent 60%),
    #f4f6fc;
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.detail-container { max-width: 1240px; }

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(18px); }
  to { opacity: 1; transform: translateY(0); }
}
@keyframes spin { to { transform: rotate(360deg); } }
@keyframes floaty {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}
@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}
@keyframes pulseDot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.35); }
}

.breadcrumb-custom { animation: fadeUp 0.45s var(--ease) both; }
.product-panel { animation: fadeUp 0.6s var(--ease) 0.05s both; }
.specifications-section { animation: fadeUp 0.6s var(--ease) 0.15s both; }
.related-section { animation: fadeUp 0.6s var(--ease) 0.2s both; }
.reviews-section { animation: fadeUp 0.6s var(--ease) 0.25s both; }

/* =========================================================
   BREADCRUMB
   ========================================================= */
.breadcrumb-custom {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 10px;
  margin: 4px 0 20px;
  color: #94a3b8;
  font-size: 12.5px;
}
.breadcrumb-custom a {
  padding: 5px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-weight: 700;
  text-decoration: none;
  transition: 0.25s var(--ease);
}
.breadcrumb-custom a:hover { color: #fff; background: var(--primary); transform: translateY(-2px); }
.breadcrumb-custom i { color: #cbd5e1; font-size: 9px; }
.breadcrumb-current {
  max-width: 420px;
  overflow: hidden;
  color: var(--ink);
  font-weight: 800;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* =========================================================
   KHUNG CHÍNH
   ========================================================= */
.product-panel,
.content-card {
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 26px;
  background: #fff;
  box-shadow: var(--shadow-md);
}
.product-panel { position: relative; padding: 30px; }
.product-panel::before {
  content: "";
  position: absolute;
  inset: 0 0 auto 0;
  height: 5px;
  background: var(--grad-primary);
}
.product-layout {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 0.94fr);
  gap: 44px;
}

/* =========================================================
   GALLERY
   ========================================================= */
.gallery-column { min-width: 0; }

.gallery-main {
  position: relative;
  display: flex;
  min-height: 440px;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: 52px 26px 44px;
  border: 1px solid #e0e7ff;
  border-radius: 22px;
  background:
    radial-gradient(circle at 50% 45%, #ffffff 0%, #f5f7ff 62%, #e8edff 100%);
}
.gallery-main::before,
.gallery-main::after {
  content: "";
  position: absolute;
  border-radius: 50%;
  pointer-events: none;
}
.gallery-main::before {
  top: -80px;
  right: -60px;
  width: 240px;
  height: 240px;
  background: radial-gradient(circle, rgba(99, 102, 241, 0.2), transparent 68%);
}
.gallery-main::after {
  bottom: -90px;
  left: -50px;
  width: 240px;
  height: 240px;
  background: radial-gradient(circle, rgba(6, 182, 212, 0.18), transparent 68%);
}
.gallery-main img {
  position: relative;
  z-index: 1;
  display: block;
  width: 100%;
  max-height: 370px;
  object-fit: contain;
  filter: drop-shadow(0 22px 24px rgba(49, 46, 129, 0.2));
  animation: floaty 6s ease-in-out infinite;
  transition: transform 0.4s var(--ease);
}
.gallery-main img:hover { transform: scale(1.06); animation-play-state: paused; }

.gallery-label {
  position: absolute;
  top: 18px;
  left: 18px;
  z-index: 2;
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 11px;
  border-radius: 999px;
  color: var(--primary);
  background: rgba(255, 255, 255, 0.85);
  backdrop-filter: blur(8px);
  box-shadow: var(--shadow-sm);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 1.2px;
}
.gallery-label i { font-size: 12px; }

.product-status {
  position: absolute;
  top: 18px;
  right: 18px;
  z-index: 2;
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 7px 12px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 800;
}
.product-status::before {
  content: "";
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: currentColor;
}
.status-active { color: #047857; background: #d1fae5; }
.status-active::before { animation: pulseDot 1.8s ease-in-out infinite; }
.status-out { color: #b45309; background: #fef3c7; }
.status-off { color: #be123c; background: #ffe4e6; }

.image-caption {
  position: absolute;
  right: 18px;
  bottom: 12px;
  left: 18px;
  z-index: 2;
  display: flex;
  justify-content: space-between;
  color: #94a3b8;
  font-size: 11px;
  font-weight: 600;
}
.no-image {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  color: #a5b4fc;
}
.no-image i { font-size: 52px; }

.gallery-thumbnails { display: flex; flex-wrap: wrap; gap: 11px; margin-top: 16px; }
.thumbnail {
  display: flex;
  width: 80px;
  height: 80px;
  align-items: center;
  justify-content: center;
  padding: 7px;
  overflow: hidden;
  border: 2px solid var(--line);
  border-radius: 16px;
  background: #fff;
  cursor: pointer;
  transition: 0.28s var(--ease);
}
.thumbnail img { width: 100%; height: 100%; object-fit: contain; }
.thumbnail:hover { transform: translateY(-4px); border-color: #c7d2fe; box-shadow: 0 10px 20px rgba(79, 70, 229, 0.15); }
.thumbnail.active {
  border-color: transparent;
  background:
    linear-gradient(#fff, #fff) padding-box,
    var(--grad-primary) border-box;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.3);
}

.trust-row { display: flex; flex-wrap: wrap; gap: 12px; margin-top: 18px; }
.trust-row > div {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 9px 14px;
  border: 1px solid #d1fae5;
  border-radius: 999px;
  color: #047857;
  background: #f0fdf7;
  font-size: 11.5px;
  font-weight: 700;
}
.trust-row i { font-size: 14px; }

/* =========================================================
   THÔNG TIN SẢN PHẨM
   ========================================================= */
.product-info-column { min-width: 0; padding: 6px 0; }

.product-eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  margin-bottom: 14px;
  padding: 5px 13px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 10.5px;
  font-weight: 900;
  letter-spacing: 1.5px;
}
.eyebrow-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: var(--primary);
  animation: pulseDot 1.8s ease-in-out infinite;
}

.product-title {
  margin: 0;
  font-size: clamp(26px, 2.6vw, 36px);
  font-weight: 900;
  line-height: 1.25;
  letter-spacing: -1px;
  overflow-wrap: anywhere;
  background: linear-gradient(90deg, #0f172a 0%, #312e81 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.product-meta {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 11px;
  margin-top: 17px;
  color: var(--muted);
  font-size: 12px;
}
.product-meta b { color: var(--ink); }
.product-meta i { margin-right: 4px; color: var(--gold); }
.product-meta small { color: #94a3b8; }
.meta-divider { width: 1px; height: 14px; background: #cbd5e1; }

.price-card {
  position: relative;
  overflow: hidden;
  margin-top: 26px;
  padding: 22px 24px;
  border: 1px solid #c7d2fe;
  border-radius: 20px;
  background: linear-gradient(120deg, #eef2ff 0%, #f0f9ff 100%);
}
.price-card::after {
  content: "";
  position: absolute;
  top: -50px;
  right: -40px;
  width: 170px;
  height: 170px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(79, 70, 229, 0.18), transparent 68%);
}
.price-label { position: relative; z-index: 1; color: var(--muted); font-size: 12px; font-weight: 700; }
.price-value {
  position: relative;
  z-index: 1;
  margin-top: 5px;
  font-size: clamp(30px, 3.4vw, 42px);
  font-weight: 900;
  letter-spacing: -1.2px;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.price-value span { font-size: 24px; }
.price-note { position: relative; z-index: 1; margin: 6px 0 0; color: #94a3b8; font-size: 11px; }

.stock-card {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-top: 16px;
  padding: 15px 16px;
  border: 1px solid #d1fae5;
  border-radius: 16px;
  background: linear-gradient(120deg, #ecfdf5, #f8fffb);
}
.stock-icon {
  display: grid;
  width: 42px;
  height: 42px;
  flex-shrink: 0;
  place-items: center;
  border-radius: 13px;
  color: #fff;
  background: var(--grad-success);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.33);
}
.stock-copy { display: flex; flex-direction: column; gap: 4px; }
.stock-copy strong { color: #065f46; font-size: 13.5px; font-weight: 800; }
.stock-copy span { color: #5b8a77; font-size: 11.5px; }

.detail-description { margin-top: 22px; }
.detail-description h3 { margin-bottom: 9px; color: var(--ink); font-size: 14.5px; font-weight: 900; }
.detail-description p {
  margin: 0;
  padding: 14px 16px;
  border-left: 4px solid #a5b4fc;
  border-radius: 0 14px 14px 0;
  color: #475569;
  background: #f8fafc;
  font-size: 13px;
  line-height: 1.85;
  white-space: pre-line;
}

/* ---- Số lượng ---- */
.quantity-section { display: flex; flex-wrap: wrap; align-items: center; gap: 13px; margin-top: 22px; }
.quantity-section > label { color: var(--ink); font-size: 13px; font-weight: 800; }
.quantity-controls {
  display: flex;
  width: 142px;
  height: 44px;
  align-items: center;
  justify-content: space-between;
  overflow: hidden;
  border: 1.5px solid var(--line);
  border-radius: 13px;
  background: #fff;
  transition: 0.25s var(--ease);
}
.quantity-controls:focus-within { border-color: var(--primary); box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12); }
.quantity-controls button {
  display: grid;
  width: 40px;
  height: 100%;
  flex-shrink: 0;
  place-items: center;
  border: 0;
  color: var(--primary);
  background: #eef2ff;
  cursor: pointer;
  transition: 0.2s;
}
.quantity-controls button:hover:not(:disabled) { color: #fff; background: var(--primary); }
.quantity-controls button:disabled { color: #cbd5e1; background: #f8fafc; cursor: not-allowed; }
.quantity-controls input {
  width: 52px;
  min-width: 0;
  border: 0;
  outline: 0;
  color: var(--ink);
  background: transparent;
  text-align: center;
  font-size: 14px;
  font-weight: 900;
  appearance: textfield;
}
.quantity-controls input::-webkit-inner-spin-button,
.quantity-controls input::-webkit-outer-spin-button { margin: 0; appearance: none; }
.quantity-hint { width: 100%; color: #94a3b8; font-size: 11px; }

/* ---- Nút hành động ---- */
.product-actions { display: grid; grid-template-columns: 1fr 1.15fr; gap: 12px; margin-top: 22px; }

.btn-buy,
.btn-add-cart,
.btn-primary-custom,
.btn-light-custom {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  min-height: 54px;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 12px 18px;
  border-radius: 14px;
  font-size: 12.5px;
  font-weight: 900;
  letter-spacing: 0.4px;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s, color 0.25s;
}

.btn-buy,
.btn-primary-custom {
  border: 0;
  color: #fff;
  background: var(--grad-hot);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 10px 24px rgba(244, 63, 94, 0.35);
}
.btn-primary-custom { background: var(--grad-primary); box-shadow: 0 10px 24px rgba(79, 70, 229, 0.32); }
.btn-buy::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 40%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  transform: translateX(-120%) skewX(-20deg);
  pointer-events: none;
}
.btn-buy:hover:not(:disabled) {
  transform: translateY(-3px);
  background-position: 100% 50%;
  box-shadow: 0 16px 32px rgba(244, 63, 94, 0.45);
}
.btn-buy:hover:not(:disabled)::after { animation: shine 0.8s var(--ease); }
.btn-buy i { animation: floaty 2.4s ease-in-out infinite; }

.btn-add-cart {
  border: 2px solid #c7d2fe;
  color: var(--primary);
  background: #fff;
}
.btn-add-cart:hover:not(:disabled) {
  transform: translateY(-3px);
  color: #fff;
  border-color: transparent;
  background: var(--grad-primary);
  box-shadow: 0 14px 28px rgba(79, 70, 229, 0.38);
}
.btn-light-custom { border: 1.5px solid var(--line); color: #475569; background: #fff; }
.btn-light-custom:hover { color: var(--primary); border-color: #c7d2fe; background: #eef2ff; transform: translateY(-2px); }
.btn-primary-custom:hover { transform: translateY(-2px); box-shadow: 0 14px 28px rgba(79, 70, 229, 0.42); }

.btn-buy:active:not(:disabled),
.btn-add-cart:active:not(:disabled) { transform: scale(0.97); }
.btn-buy:disabled,
.btn-add-cart:disabled { opacity: 0.5; cursor: not-allowed; box-shadow: none; filter: grayscale(0.3); }

.action-warning {
  margin: 12px 0 0;
  padding: 10px 14px;
  border-radius: 12px;
  color: #be123c;
  background: #fff1f2;
  font-size: 12px;
  font-weight: 600;
}

.service-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-top: 24px;
  padding-top: 22px;
  border-top: 1px dashed #cbd5e1;
}
.service-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: #f8fafc;
  transition: 0.28s var(--ease);
}
.service-item:hover { transform: translateY(-3px); border-color: #c7d2fe; background: #fff; box-shadow: var(--shadow-md); }
.service-icon {
  display: grid;
  width: 40px;
  height: 40px;
  flex-shrink: 0;
  place-items: center;
  border-radius: 12px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
}
.service-icon i { font-size: 15px; }
.service-item div:last-child { display: flex; flex-direction: column; gap: 4px; }
.service-item strong { color: var(--ink); font-size: 12px; font-weight: 800; }
.service-item span { color: var(--muted); font-size: 10.5px; line-height: 1.5; }

/* =========================================================
   THẺ NỘI DUNG CHUNG
   ========================================================= */
.content-card { margin-top: 26px; padding: 30px; }

.section-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 26px;
}
.section-kicker {
  display: inline-flex;
  padding: 4px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 10px;
  font-weight: 900;
  letter-spacing: 1.5px;
}
.section-heading h2 {
  margin: 10px 0 6px;
  font-size: 26px;
  font-weight: 900;
  letter-spacing: -0.6px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.section-heading p { margin: 0; color: var(--muted); font-size: 12.5px; }

.section-heading-icon {
  display: grid;
  width: 56px;
  height: 56px;
  flex-shrink: 0;
  place-items: center;
  border-radius: 18px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 10px 22px rgba(79, 70, 229, 0.35);
  font-size: 21px;
  transition: transform 0.35s var(--ease);
}
.content-card:hover .section-heading-icon { transform: rotate(-8deg) scale(1.08); }
.rating-heading-icon { background: linear-gradient(135deg, #fbbf24, #f97316); box-shadow: 0 10px 22px rgba(245, 158, 11, 0.4); }

/* =========================================================
   THÔNG SỐ
   ========================================================= */
.spec-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.spec-item {
  display: grid;
  grid-template-columns: 34px minmax(100px, 0.75fr) minmax(0, 1.25fr);
  align-items: center;
  gap: 11px;
  min-height: 62px;
  padding: 13px 14px;
  border: 1px solid #eef2f7;
  border-radius: 16px;
  background: #f8fafc;
  transition: 0.28s var(--ease);
}
.spec-item:hover {
  transform: translateY(-3px);
  border-color: #c7d2fe;
  background: #fff;
  box-shadow: 0 10px 22px rgba(79, 70, 229, 0.12);
}
.spec-index {
  display: grid;
  width: 30px;
  height: 30px;
  place-items: center;
  border-radius: 9px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 10.5px;
  font-weight: 800;
}
.spec-name { color: var(--muted); font-size: 12px; font-weight: 600; }
.spec-value { color: var(--ink); font-size: 12.5px; font-weight: 800; text-align: right; overflow-wrap: anywhere; }

.empty-state { padding: 34px 10px; color: var(--muted); text-align: center; font-size: 13px; }
.empty-state i { margin-bottom: 10px; color: #a5b4fc; font-size: 28px; }
.empty-state p { margin: 0; }

/* =========================================================
   SẢN PHẨM LIÊN QUAN
   ========================================================= */
.view-more-link {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0;
  padding: 9px 16px;
  border-radius: 999px;
  color: var(--primary);
  background: #eef2ff;
  font-size: 12px;
  font-weight: 800;
  text-decoration: none;
  transition: 0.28s var(--ease);
}
.view-more-link:hover { color: #fff; background: var(--grad-primary); transform: translateX(4px); }

.related-grid { display: grid; grid-template-columns: repeat(5, minmax(0, 1fr)); gap: 16px; }
.related-card {
  position: relative;
  min-width: 0;
  padding: 0;
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 20px;
  background: #fff;
  text-align: left;
  cursor: pointer;
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease), border-color 0.3s;
}
.related-card::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 4px;
  background: var(--grad-primary);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.45s var(--ease);
}
.related-card:hover { transform: translateY(-8px); border-color: transparent; box-shadow: 0 20px 40px rgba(49, 46, 129, 0.16); }
.related-card:hover::after { transform: scaleX(1); }

.related-image {
  position: relative;
  display: grid;
  height: 160px;
  place-items: center;
  padding: 16px;
  background: radial-gradient(circle at 50% 50%, #fff 0%, #f1f4ff 100%);
}
.related-image img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  transition: transform 0.4s var(--ease);
}
.related-card:hover .related-image img { transform: scale(1.1) rotate(-2deg); }
.related-stock {
  position: absolute;
  top: 10px;
  left: 10px;
  padding: 5px 9px;
  border-radius: 999px;
  color: #b45309;
  background: #fef3c7;
  font-size: 9.5px;
  font-weight: 800;
}
.related-content { padding: 15px; }
.related-code { color: #94a3b8; font-size: 10px; font-weight: 800; letter-spacing: 0.6px; }
.related-content h3 {
  display: -webkit-box;
  min-height: 40px;
  margin: 7px 0 11px;
  overflow: hidden;
  color: var(--ink);
  font-size: 12.5px;
  font-weight: 800;
  line-height: 1.6;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}
.related-price {
  display: block;
  font-size: 15px;
  font-weight: 900;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.related-link {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 14px;
  padding-top: 12px;
  border-top: 1px dashed #e2e8f0;
  color: var(--muted);
  font-size: 10.5px;
  font-weight: 700;
}
.related-link i { color: var(--primary); transition: transform 0.25s var(--ease); }
.related-card:hover .related-link i { transform: translateX(5px); }

/* =========================================================
   ĐÁNH GIÁ
   ========================================================= */
.rating-summary {
  display: grid;
  grid-template-columns: 0.7fr 1.3fr;
  gap: 34px;
  padding: 26px;
  border: 1px solid #fde68a;
  border-radius: 22px;
  background: linear-gradient(120deg, #fffbeb 0%, #fff 100%);
}
.rating-overall {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
  border-right: 1px dashed #fcd34d;
}
.rating-number {
  font-size: 52px;
  font-weight: 900;
  letter-spacing: -2px;
  background: linear-gradient(135deg, #f59e0b, #f97316);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.rating-number span {
  font-size: 16px;
  font-weight: 700;
  letter-spacing: 0;
  -webkit-text-fill-color: #94a3b8;
}
.stars-row { display: flex; gap: 5px; margin: 6px 0 10px; color: #f59e0b; font-size: 16px; }
.star-muted { color: #d9dee5 !important; }
.rating-count { color: var(--muted); font-size: 11.5px; font-weight: 600; }

.rating-breakdown { display: flex; flex-direction: column; justify-content: center; gap: 12px; }
.rating-bar-row {
  display: grid;
  grid-template-columns: 46px minmax(0, 1fr) 40px;
  align-items: center;
  gap: 11px;
  color: var(--muted);
  font-size: 11.5px;
  font-weight: 700;
}
.rating-track { height: 9px; overflow: hidden; border-radius: 10px; background: #fef3c7; }
.rating-fill {
  height: 100%;
  border-radius: inherit;
  background: linear-gradient(90deg, #fbbf24, #f97316);
  transition: width 0.6s var(--ease);
}
.rating-percent { color: #94a3b8; text-align: right; }

.review-form-card {
  margin-top: 26px;
  padding: 24px;
  border: 1px solid #e0e7ff;
  border-radius: 22px;
  background: linear-gradient(180deg, #f8faff, #fff);
}
.review-form-heading { display: flex; align-items: center; gap: 13px; margin-bottom: 22px; }
.review-form-icon {
  display: grid;
  width: 46px;
  height: 46px;
  flex-shrink: 0;
  place-items: center;
  border-radius: 14px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.32);
  font-size: 18px;
}
.review-form-heading h3 { margin: 0 0 5px; color: var(--ink); font-size: 15.5px; font-weight: 900; }
.review-form-heading p { margin: 0; color: var(--muted); font-size: 11.5px; }

.review-star-picker { display: flex; flex-wrap: wrap; align-items: center; gap: 12px; margin-bottom: 14px; }
.review-star-picker > span { color: #475569; font-size: 12.5px; font-weight: 700; }
.star-buttons { display: inline-flex; gap: 4px; }
.star-buttons button {
  padding: 2px;
  border: 0;
  color: #d5dbe3;
  background: transparent;
  cursor: pointer;
  font-size: 24px;
  transition: transform 0.2s var(--ease);
}
.star-buttons button:hover { transform: scale(1.3) rotate(-8deg); }
.star-buttons button .selected { color: #f59e0b; filter: drop-shadow(0 3px 5px rgba(245, 158, 11, 0.4)); }
.review-star-picker > strong {
  padding: 4px 11px;
  border-radius: 999px;
  color: #b45309;
  background: #fef3c7;
  font-size: 12px;
}

.review-textarea {
  display: block;
  width: 100%;
  min-height: 112px;
  padding: 14px 16px;
  resize: vertical;
  outline: 0;
  border: 1.5px solid var(--line);
  border-radius: 14px;
  color: #1e293b;
  background: #fff;
  font-family: inherit;
  font-size: 13px;
  line-height: 1.7;
  transition: all 0.25s var(--ease);
}
.review-textarea:hover { border-color: #c7d2fe; }
.review-textarea:focus { border-color: var(--primary); box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12); }
.review-textarea::placeholder { color: #a0aabc; }

.review-form-footer { display: flex; align-items: center; justify-content: space-between; gap: 12px; margin-top: 14px; }
.review-form-footer > span { color: #94a3b8; font-size: 11.5px; }

.btn-submit-review {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  min-height: 44px;
  align-items: center;
  justify-content: center;
  gap: 9px;
  padding: 0 20px;
  border: 0;
  border-radius: 12px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
  font-size: 12px;
  font-weight: 800;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.btn-submit-review:hover:not(:disabled) { transform: translateY(-2px); box-shadow: 0 14px 26px rgba(79, 70, 229, 0.42); }
.btn-submit-review:disabled { cursor: not-allowed; opacity: 0.55; box-shadow: none; }

.login-review-note {
  margin: 14px 0 0;
  padding: 9px 13px;
  border-radius: 10px;
  color: #92400e;
  background: #fffbeb;
  font-size: 11.5px;
}

.review-list-heading { display: flex; align-items: center; justify-content: space-between; gap: 12px; margin: 32px 0 12px; }
.review-list-heading h3 { margin: 0; color: var(--ink); font-size: 17px; font-weight: 900; }
.review-list-heading span {
  padding: 6px 12px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 11.5px;
  font-weight: 700;
}

.review-list { display: flex; flex-direction: column; gap: 12px; }
.review-card {
  display: flex;
  gap: 14px;
  padding: 18px;
  border: 1px solid #eef2f7;
  border-radius: 18px;
  background: #fff;
  animation: fadeUp 0.45s var(--ease) both;
  transition: 0.28s var(--ease);
}
.review-card:hover { transform: translateX(5px); border-color: #c7d2fe; background: #fafbff; box-shadow: 0 10px 24px rgba(79, 70, 229, 0.1); }
.review-avatar {
  display: grid;
  width: 46px;
  height: 46px;
  flex-shrink: 0;
  place-items: center;
  border: 2px solid #fff;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.2);
  font-size: 16px;
  font-weight: 900;
}
.review-body { flex: 1; min-width: 0; }
.review-topline { display: flex; flex-wrap: wrap; align-items: flex-start; justify-content: space-between; gap: 10px; }
.review-author { color: var(--ink); font-size: 13.5px; font-weight: 800; }
.review-stars { display: flex; gap: 3px; margin-top: 6px; color: #f59e0b; font-size: 11px; }
.review-date { padding: 4px 10px; border-radius: 999px; color: #94a3b8; background: var(--soft); font-size: 11px; }
.review-content { margin: 12px 0 0; color: #475569; font-size: 13px; line-height: 1.8; overflow-wrap: anywhere; white-space: pre-line; }

.empty-reviews { padding: 38px 10px 22px; text-align: center; }
.empty-review-icon {
  display: grid;
  width: 76px;
  height: 76px;
  margin: 0 auto 16px;
  place-items: center;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 30px;
}
.empty-reviews h3 { margin: 0 0 7px; color: var(--ink); font-size: 16px; font-weight: 900; }
.empty-reviews p { margin: 0; color: var(--muted); font-size: 12.5px; }

.review-loading { display: flex; align-items: center; justify-content: center; gap: 10px; padding: 28px 0; color: var(--muted); font-size: 12.5px; }
.review-error { margin: 14px 0 0; padding: 10px 14px; border-radius: 12px; color: #be123c; background: #fff1f2; font-size: 12px; font-weight: 600; }

/* =========================================================
   TRẠNG THÁI TRANG (loading / lỗi)
   ========================================================= */
.page-state {
  display: flex;
  min-height: 55vh;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 13px;
  text-align: center;
}
.page-state h2 { margin: 0; color: var(--ink); font-size: 24px; font-weight: 900; }
.page-state p { margin: 0; color: var(--muted); font-size: 13px; }
.state-icon {
  display: grid;
  width: 82px;
  height: 82px;
  place-items: center;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 32px;
}
.loading-spinner {
  width: 44px;
  height: 44px;
  border: 4px solid #e0e7ff;
  border-top-color: var(--primary);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
.small-spinner { width: 20px; height: 20px; border-width: 3px; }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 991px) {
  .product-layout { grid-template-columns: 1fr; gap: 32px; }
  .gallery-main { min-height: 380px; }
  .product-info-column { padding: 0; }
  .related-grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }
}

@media (max-width: 767px) {
  .detail-page { padding-top: 10px; }
  .product-panel,
  .content-card { padding: 18px; border-radius: 20px; }

  .gallery-main { min-height: 300px; padding: 48px 14px; }
  .gallery-main img { max-height: 250px; }
  .product-title { font-size: 25px; }
  .product-actions { grid-template-columns: 1fr; }
  .service-grid { grid-template-columns: 1fr; }
  .spec-grid { grid-template-columns: 1fr; }
  .spec-item { grid-template-columns: 30px minmax(95px, 0.8fr) minmax(0, 1.2fr); }

  .rating-summary { grid-template-columns: 1fr; gap: 20px; padding: 18px; }
  .rating-overall { padding-bottom: 20px; border-right: 0; border-bottom: 1px dashed #fcd34d; }

  .related-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 11px; }
  .related-image { height: 130px; }
  .related-content { padding: 12px; }

  .section-heading h2 { font-size: 21px; }
  .section-heading-icon { width: 44px; height: 44px; border-radius: 14px; font-size: 17px; }
  .review-card { padding: 14px; }
}

@media (max-width: 400px) {
  .trust-row { flex-direction: column; gap: 10px; }
  .quantity-section { gap: 10px; }
  .review-form-card { padding: 16px; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .detail-page *,
  .detail-page *::before,
  .detail-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>