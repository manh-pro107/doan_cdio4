
<template>
  <main class="checkout-page">
    <div class="ambient ambient-one"></div>
    <div class="ambient ambient-two"></div>

    <div class="checkout-shell">
      <!-- HEADER -->
      <header class="shop-header">
        <button class="brand" type="button" @click="goHome">
          <span class="brand-mark">T<span>.</span></span>
          <span class="brand-copy">
            <strong>TECHSHOP</strong>
            <small>TECHNOLOGY STORE</small>
          </span>
        </button>

        <div class="header-trust">
          <span class="trust-icon">✓</span>
          <div>
            <strong>Đặt hàng an toàn</strong>
            <small>Thông tin được lưu theo đơn hàng</small>
          </div>
        </div>

        <button class="back-button" type="button" @click="goBack">
          <span>←</span>
          {{ isBuyNow ? "Quay lại sản phẩm" : "Quay lại giỏ hàng" }}
        </button>
      </header>

      <!-- PAGE INTRO -->
      <section class="page-intro">
        <div>
          <div class="eyebrow">
            <span class="eyebrow-line"></span>
            TECHSHOP / ĐẶT HÀNG
          </div>

          <h1>
            {{ isBuyNow ? "Sản phẩm bạn chọn." : "Sẵn sàng đặt hàng." }}
          </h1>

          <p>
            {{
              isBuyNow
                ? "Kiểm tra sản phẩm và thông tin nhận hàng trước khi gửi đơn."
                : "Kiểm tra giỏ hàng, áp dụng voucher và xác nhận thông tin nhận hàng."
            }}
          </p>
        </div>

        <div class="purchase-mode" :class="{ single: isBuyNow }">
          <span class="mode-dot"></span>
          {{ isBuyNow ? "MUA RIÊNG" : "ĐẶT TOÀN BỘ GIỎ" }}
        </div>
      </section>

      <!-- PROGRESS -->
      <nav class="steps" aria-label="Quy trình đặt hàng">
        <div class="step is-active">
          <span class="step-number">01</span>
          <div>
            <strong>Thông tin</strong>
            <small>Người nhận hàng</small>
          </div>
        </div>

        <span class="step-line"></span>

        <div class="step is-active">
          <span class="step-number">02</span>
          <div>
            <strong>Phương thức</strong>
            <small>COD hoặc chuyển khoản</small>
          </div>
        </div>

        <span class="step-line"></span>

        <div class="step">
          <span class="step-number">03</span>
          <div>
            <strong>Đặt hàng</strong>
            <small>Chờ xác nhận</small>
          </div>
        </div>
      </nav>

      <!-- FEEDBACK -->
      <div v-if="error" class="notice notice-error" role="alert">
        <span class="notice-symbol">!</span>
        <p>{{ error }}</p>
        <button type="button" aria-label="Đóng thông báo" @click="error = ''">
          ×
        </button>
      </div>

      <div v-if="successMessage && !orderSuccess" class="notice notice-success">
        <span class="notice-symbol">✓</span>
        <p>{{ successMessage }}</p>
      </div>

      <!-- ORDER SUCCESS -->
      <section v-if="orderSuccess" class="success-screen">
        <div class="success-visual">
          <span class="success-check">✓</span>
        </div>

        <div class="eyebrow success-eyebrow">
          <span class="eyebrow-line"></span>
          ĐƠN HÀNG ĐÃ ĐƯỢC GHI NHẬN
        </div>

        <h2>Đặt hàng thành công!</h2>

        <p class="success-description">
          TECHSHOP đã nhận được yêu cầu đặt hàng của bạn. Đơn hàng hiện đang chờ
          cửa hàng xác nhận.
        </p>

        <div class="success-order-number">
          <span>MÃ ĐƠN HÀNG</span>
          <strong>#{{ orderSuccess.MaDH || "Đã ghi nhận" }}</strong>
          <span class="pending-pill"> <i></i> Chờ xác nhận </span>
        </div>

        <div class="success-details">
          <div>
            <span>Người nhận</span>
            <strong>{{ form.TenNguoiNhan }}</strong>
          </div>
          <div>
            <span>Phương thức</span>
            <strong>
              {{
                paymentMethod === "COD"
                  ? "Thanh toán khi nhận hàng"
                  : "Chuyển khoản ngân hàng"
              }}
            </strong>
          </div>
          <div class="detail-wide">
            <span>Địa chỉ giao hàng</span>
            <strong>{{ form.DiaChiGiaoHang }}</strong>
          </div>
          <div>
            <span>Tổng tiền đơn hàng</span>
            <strong class="success-total">{{
              formatMoney(orderSuccess.TongTien)
            }}</strong>
          </div>
        </div>

        <!-- QR only when bank transfer is chosen -->
        <section v-if="paymentMethod === 'ChuyenKhoan'" class="success-bank">
          <div class="bank-heading">
            <div>
              <span class="bank-eyebrow">BANK TRANSFER</span>
              <h3>Thông tin chuyển khoản</h3>
            </div>
            <span class="bank-label">QR PAY</span>
          </div>

          <div class="bank-layout">
            <div class="qr-frame">
              <img
                v-if="!qrImageBroken"
                :src="qrImage"
                alt="Mã QR chuyển khoản của TECHSHOP"
                @error="qrImageBroken = true"
              />
              <div v-else class="qr-missing">
                <span>▦</span>
                <strong>Chưa có ảnh QR</strong>
                <small>Kiểm tra đường dẫn ảnh trong code.</small>
              </div>
            </div>

            <div class="bank-information">
              <div>
                <span>Ngân hàng</span>
                <strong>{{ bankInfo.bankName }}</strong>
              </div>
              <div>
                <span>Chủ tài khoản</span>
                <strong>{{ bankInfo.accountName }}</strong>
              </div>
              <div>
                <span>Số tài khoản</span>
                <strong>{{ bankInfo.accountNumber }}</strong>
              </div>
              <div>
                <span>Số tiền dự kiến</span>
                <strong class="bank-amount">{{
                  formatMoney(orderSuccess.TongTien)
                }}</strong>
              </div>
              <div>
                <span>Nội dung chuyển khoản</span>
                <strong class="transfer-code">{{ transferContent }}</strong>
              </div>

              <button
                class="copy-button"
                type="button"
                @click="copyTransferContent"
              >
                {{
                  copied ? "Đã sao chép ✓" : "Sao chép nội dung chuyển khoản"
                }}
              </button>
            </div>
          </div>

          <p class="bank-warning">
            Hiển thị QR không đồng nghĩa với đã thanh toán. Cửa hàng cần kiểm
            tra giao dịch thực tế và xác nhận thủ công.
          </p>
        </section>

        <div class="success-actions">
          <button class="button-primary" type="button" @click="goHistory">
            Xem lịch sử mua hàng <span>→</span>
          </button>
          <button class="button-secondary" type="button" @click="goHome">
            Tiếp tục mua sắm
          </button>
        </div>
      </section>

      <!-- CHECKOUT FORM -->
      <div v-else class="checkout-layout">
        <div class="checkout-main">
          <form id="place-order-form" @submit.prevent="placeOrder" novalidate>
            <!-- RECIPIENT -->
            <section class="surface-card">
              <div class="section-heading">
                <span class="section-number">01</span>
                <div class="section-heading-copy">
                  <h2>Thông tin nhận hàng</h2>
                  <p>Nhập chính xác thông tin để cửa hàng xử lý đơn hàng.</p>
                </div>
                <span class="required-note"><i></i> Bắt buộc</span>
              </div>

              <div v-if="profileLoading" class="inline-loading">
                <span class="spinner"></span>
                Đang tải thông tin tài khoản...
              </div>

              <p v-if="profileHint" class="inline-hint">{{ profileHint }}</p>

              <div class="fields-grid">
                <label class="field">
                  <span>Họ tên người nhận <b>*</b></span>
                  <input
                    v-model.trim="form.TenNguoiNhan"
                    type="text"
                    autocomplete="name"
                    maxlength="100"
                    placeholder="Nguyễn Văn An"
                    required
                  />
                </label>

                <label class="field">
                  <span>Số điện thoại <b>*</b></span>
                  <input
                    v-model.trim="form.SoDienThoaiNhan"
                    type="tel"
                    autocomplete="tel"
                    inputmode="tel"
                    maxlength="15"
                    placeholder="09xx xxx xxx"
                    required
                  />
                </label>

                <label class="field field-full">
                  <span>Địa chỉ giao hàng <b>*</b></span>
                  <textarea
                    v-model.trim="form.DiaChiGiaoHang"
                    autocomplete="street-address"
                    rows="3"
                    maxlength="500"
                    placeholder="Số nhà, đường, phường/xã, quận/huyện, tỉnh/thành"
                    required
                  ></textarea>
                  <small>
                    Bạn có thể chỉnh địa chỉ cho đơn này. Địa chỉ của các đơn cũ
                    không bị thay đổi.
                  </small>
                </label>
              </div>

              <div class="privacy-note">
                <span>i</span>
                <p>
                  Thông tin nhận hàng được lưu cùng đơn hàng để cửa hàng xử lý.
                </p>
              </div>
            </section>

            <!-- VOUCHER -->
            <section class="surface-card">
              <div class="section-heading">
                <span class="section-number">02</span>
                <div class="section-heading-copy">
                  <h2>Ưu đãi dành cho bạn</h2>
                  <p>Nhập mã giảm giá của TECHSHOP nếu bạn có.</p>
                </div>
                <span class="voucher-icon">%</span>
              </div>

              <div class="voucher-entry">
                <div class="voucher-input-wrap">
                  <span class="ticket-icon">✳</span>
                  <input
                    v-model.trim="voucherCode"
                    type="text"
                    maxlength="50"
                    placeholder="Nhập mã voucher"
                    :disabled="voucherLoading || !!appliedVoucher"
                    @keyup.enter.prevent="applyVoucher"
                  />
                  <button
                    v-if="appliedVoucher"
                    type="button"
                    class="voucher-remove"
                    @click="removeVoucher"
                  >
                    Gỡ mã
                  </button>
                  <button
                    v-else
                    type="button"
                    class="voucher-apply"
                    :disabled="voucherLoading || !voucherCode.trim()"
                    @click="applyVoucher"
                  >
                    {{ voucherLoading ? "Đang kiểm tra..." : "Áp dụng" }}
                  </button>
                </div>

                <p v-if="voucherError" class="voucher-message voucher-error">
                  {{ voucherError }}
                </p>

                <div v-if="appliedVoucher" class="voucher-applied">
                  <span class="voucher-applied-check">✓</span>
                  <div>
                    <strong>{{ appliedVoucher.MaGiamGia }}</strong>
                    <small
                      >Đã áp dụng giảm {{ Number(appliedVoucher.GiaTriGiam) }}%
                      theo voucher.</small
                    >
                  </div>
                  <strong class="voucher-saving"
                    >−{{ formatMoney(discountAmount) }}</strong
                  >
                </div>

                <p v-else class="voucher-help">
                  Mã giảm giá chỉ được chấp nhận khi còn hiệu lực và hợp lệ với
                  hệ thống.
                </p>
              </div>
            </section>

            <!-- PAYMENT -->
            <section class="surface-card">
              <div class="section-heading">
                <span class="section-number">03</span>
                <div class="section-heading-copy">
                  <h2>Phương thức thanh toán</h2>
                  <p>Chọn cách thanh toán phù hợp với đơn hàng của bạn.</p>
                </div>
              </div>

              <div class="payment-options">
                <button
                  type="button"
                  class="payment-option"
                  :class="{ selected: paymentMethod === 'COD' }"
                  @click="paymentMethod = 'COD'"
                >
                  <span class="payment-icon cash-icon">₫</span>
                  <span class="payment-copy">
                    <strong>Thanh toán khi nhận hàng</strong>
                    <small>COD · Trả tiền khi nhận sản phẩm</small>
                  </span>
                  <span class="radio-mark">
                    <i v-if="paymentMethod === 'COD'"></i>
                  </span>
                </button>

                <button
                  type="button"
                  class="payment-option"
                  :class="{ selected: paymentMethod === 'ChuyenKhoan' }"
                  @click="paymentMethod = 'ChuyenKhoan'"
                >
                  <span class="payment-icon bank-icon">▦</span>
                  <span class="payment-copy">
                    <strong>Chuyển khoản ngân hàng</strong>
                    <small>Quét ảnh QR và chuyển khoản thủ công</small>
                  </span>
                  <span class="radio-mark">
                    <i v-if="paymentMethod === 'ChuyenKhoan'"></i>
                  </span>
                </button>
              </div>

              <div v-if="paymentMethod === 'COD'" class="payment-explainer">
                <span class="explain-icon">✓</span>
                <p>
                  Bạn chưa cần chuyển khoản trước. Đơn hàng được tạo ở trạng
                  thái
                  <strong>Chờ xác nhận</strong>; trạng thái thu tiền được xử lý
                  theo quy trình mô phỏng.
                </p>
              </div>

              <div v-else class="qr-panel">
                <div class="qr-panel-heading">
                  <div>
                    <span class="bank-eyebrow">BANK TRANSFER</span>
                    <h3>Quét mã QR ngân hàng</h3>
                    <p>Chuyển khoản thủ công sau khi đơn hàng được ghi nhận.</p>
                  </div>
                  <span class="bank-label">QR PAY</span>
                </div>

                <div class="bank-layout">
                  <div class="qr-frame">
                    <img
                      v-if="!qrImageBroken"
                      :src="qrImage"
                      alt="Mã QR chuyển khoản của TECHSHOP"
                      @error="qrImageBroken = true"
                    />
                    <div v-else class="qr-missing">
                      <span>▦</span>
                      <strong>Chưa có ảnh QR</strong>
                      <small>Đặt ảnh tại public/images/qr-techshop.png</small>
                    </div>
                  </div>

                  <div class="bank-information">
                    <div>
                      <span>Ngân hàng</span>
                      <strong>{{ bankInfo.bankName }}</strong>
                    </div>
                    <div>
                      <span>Chủ tài khoản</span>
                      <strong>{{ bankInfo.accountName }}</strong>
                    </div>
                    <div>
                      <span>Số tài khoản</span>
                      <strong>{{ bankInfo.accountNumber }}</strong>
                    </div>
                    <div>
                      <span>Số tiền dự kiến</span>
                      <strong class="bank-amount">{{
                        formatMoney(totalAfterDiscount)
                      }}</strong>
                    </div>
                    <p class="bank-warning">
                      Mã đơn sẽ được hiển thị sau khi bạn gửi đơn thành công.
                    </p>
                  </div>
                </div>

                <div class="payment-explainer">
                  <span class="explain-icon">i</span>
                  <p>
                    Đây là chuyển khoản thủ công, chưa tích hợp cổng thanh toán.
                    Đặt hàng thành công không có nghĩa là ngân hàng đã xác nhận
                    nhận tiền.
                  </p>
                </div>
              </div>
            </section>
          </form>
        </div>

        <!-- ORDER SUMMARY -->
        <aside class="checkout-aside">
          <section class="summary-card">
            <div class="summary-heading">
              <div>
                <span class="summary-eyebrow">ORDER SUMMARY</span>
                <h2>
                  {{ isBuyNow ? "Sản phẩm đã chọn" : "Đơn hàng của bạn" }}
                </h2>
                <p>
                  {{
                    isBuyNow
                      ? "Bạn đang đặt riêng một sản phẩm."
                      : "Kiểm tra các sản phẩm trước khi gửi đơn."
                  }}
                </p>
              </div>
              <span class="item-count"
                >{{ totalUnits }} <small>món</small></span
              >
            </div>

            <div v-if="cartLoading" class="summary-state">
              <span class="spinner"></span> Đang tải sản phẩm...
            </div>

            <div v-else-if="cart.length === 0" class="empty-cart">
              <div class="empty-cart-icon">▱</div>
              <strong>
                {{
                  isBuyNow ? "Chưa tìm thấy sản phẩm" : "Giỏ hàng đang trống"
                }}
              </strong>
              <p>
                {{
                  isBuyNow
                    ? "Hãy quay lại trang sản phẩm để chọn mặt hàng cần mua."
                    : "Hãy thêm sản phẩm vào giỏ trước khi đặt hàng."
                }}
              </p>
              <button
                class="button-secondary full-button"
                type="button"
                @click="goHome"
              >
                Khám phá sản phẩm
              </button>
            </div>

            <div v-else class="summary-items">
              <article
                v-for="item in cart"
                :key="item.key"
                class="summary-item"
              >
                <div class="product-thumb">
                  <img
                    v-if="item.image && !brokenImages.includes(item.key)"
                    :src="imageUrl(item.image)"
                    :alt="item.name"
                    @error="markImageBroken(item.key)"
                  />
                  <span v-else class="image-fallback">TS</span>
                  <span class="quantity-badge">{{ item.qty }}</span>
                </div>

                <div class="product-copy">
                  <strong>{{ item.name }}</strong>
                  <small>{{ formatMoney(item.price) }} / sản phẩm</small>

                  <div v-if="isBuyNow" class="quantity-control">
                    <button
                      type="button"
                      :disabled="item.qty <= 1"
                      @click="changeSingleQty(-1)"
                    >
                      −
                    </button>
                    <span>{{ item.qty }}</span>
                    <button
                      type="button"
                      :disabled="item.qty >= item.soLuongKhaDung"
                      @click="changeSingleQty(1)"
                    >
                      +
                    </button>
                  </div>
                </div>

                <strong class="line-total">
                  {{ formatMoney(item.price * item.qty) }}
                </strong>
              </article>
            </div>

            <div v-if="cart.length" class="price-summary">
              <div class="price-line">
                <span>Tạm tính</span>
                <strong>{{ formatMoney(subtotal) }}</strong>
              </div>

              <div class="price-line">
                <span>Phí giao hàng</span>
                <span class="free-shipping">Miễn phí*</span>
              </div>

              <div class="price-line">
                <span>Voucher</span>
                <strong v-if="appliedVoucher" class="discount-text">
                  −{{ formatMoney(discountAmount) }}
                </strong>
                <span v-else class="muted-value">Chưa áp dụng</span>
              </div>

              <div class="summary-divider"></div>

              <div class="grand-total">
                <div>
                  <span>Tổng tiền dự kiến</span>
                  <small>Đơn hàng chờ cửa hàng xác nhận</small>
                </div>

                <div class="grand-total-prices">
                  <del v-if="appliedVoucher && discountAmount > 0">
                    {{ formatMoney(subtotal) }}
                  </del>

                  <strong>
                    {{ formatMoney(totalAfterDiscount) }}
                  </strong>
                </div>
              </div>

              <p class="server-note">
                Backend sẽ tính lại giá, tồn kho và voucher trước khi tạo đơn.
              </p>
            </div>

            <button
              v-if="cart.length"
              class="place-order-button"
              type="submit"
              form="place-order-form"
              :disabled="submitting || cartLoading"
            >
              <span v-if="submitting" class="spinner spinner-light"></span>
              <span>{{
                submitting ? "Đang gửi đơn hàng..." : "Đặt hàng"
              }}</span>
              <span v-if="!submitting" class="submit-arrow">→</span>
            </button>

            <div class="order-note">
              <span>✓</span>
              Đây là thao tác đặt hàng, chưa phải xác nhận thanh toán.
            </div>
          </section>

          <section class="support-card">
            <span class="support-mark">?</span>
            <div>
              <strong>Kiểm tra trước khi đặt</strong>
              <p>
                Đảm bảo số điện thoại và địa chỉ chính xác để cửa hàng có thể
                liên hệ khi xử lý đơn.
              </p>
            </div>
          </section>
        </aside>
      </div>

      <footer class="page-footer">
        <strong>TECHSHOP</strong>
        <span>Mua sắm công nghệ, rõ ràng từ lúc đặt hàng.</span>
        <span>ĐẶT HÀNG · CHỜ XÁC NHẬN</span>
      </footer>
    </div>
  </main>
</template>

<script>
import axios from "axios";

const API_ROOT = "http://127.0.0.1:8000";
const API = `${API_ROOT}/api`;

export default {
  name: "ThanhToan",

  data() {
    return {
      cart: [],
      cartLoading: false,
      profileLoading: false,
      submitting: false,
      voucherLoading: false,

      error: "",
      successMessage: "",
      profileHint: "",
      voucherError: "",

      voucherCode: "",
      appliedVoucher: null,
      orderSuccess: null,

      paymentMethod: "COD",

      brokenImages: [],
      qrImageBroken: false,
      copied: false,

      // Đặt ảnh QR thật của cửa hàng vào public/images/qr-techshop.png.
      qrImage: "/images/qr-techshop.png",

      // Thay bằng thông tin tài khoản nhận tiền thực tế của cửa hàng.
      bankInfo: {
        bankName: "Tên ngân hàng",
        accountName: "TÊN CHỦ TÀI KHOẢN",
        accountNumber: "SỐ TÀI KHOẢN",
      },

      form: {
        TenNguoiNhan: "",
        SoDienThoaiNhan: "",
        DiaChiGiaoHang: "",
      },
    };
  },

  computed: {
    isBuyNow() {
      return String(this.$route?.query?.mode || "").toLowerCase() === "single";
    },

    subtotal() {
      return this.cart.reduce(
        (sum, item) => sum + Number(item.price || 0) * Number(item.qty || 0),
        0
      );
    },

    discountAmount() {
      if (!this.appliedVoucher) return 0;

      const rawPercent = Number(this.appliedVoucher.GiaTriGiam);

      if (!Number.isFinite(rawPercent) || rawPercent <= 0) {
        return 0;
      }

      const percent = Math.min(rawPercent, 100);

      return Math.min(
        this.subtotal,
        Math.round((this.subtotal * percent) / 100)
      );
    },

    totalAfterDiscount() {
      return Math.max(0, this.subtotal - this.discountAmount);
    },

    totalUnits() {
      return this.cart.reduce((sum, item) => sum + Number(item.qty || 0), 0);
    },

    transferContent() {
      return `TECHSHOP DH${this.orderSuccess?.MaDH || ""}`;
    },
  },

  watch: {
    "$route.query.mode"() {
      this.resetCheckoutState();
      this.loadCheckoutItems();
    },

    "$route.query.MaSP"() {
      if (this.isBuyNow) {
        this.resetCheckoutState();
        this.loadCheckoutItems();
      }
    },

    "$route.query.SoLuong"() {
      if (this.isBuyNow) {
        this.resetCheckoutState();
        this.loadCheckoutItems();
      }
    },
  },

  mounted() {
    this.loadProfile();
    this.loadCheckoutItems();
  },

  methods: {
    getToken() {
      return (
        localStorage.getItem("token") ||
        localStorage.getItem("client_token") ||
        localStorage.getItem("user_token")
      );
    },

    requestConfig() {
      const token = this.getToken();

      return {
        headers: token ? { Authorization: `Bearer ${token}` } : {},
      };
    },

    unwrap(data) {
      let value = data?.data ?? data?.user ?? data?.nguoiDung ?? data;

      if (value?.user) value = value.user;
      if (value?.nguoiDung) value = value.nguoiDung;

      return value || {};
    },

    async loadProfile() {
      this.profileLoading = true;
      this.profileHint = "";

      try {
        try {
          const savedUser = JSON.parse(localStorage.getItem("user") || "null");
          if (savedUser) this.fillProfile(savedUser);
        } catch (_) {
          // Tiếp tục lấy từ API.
        }

        const response = await axios.get(
          `${API}/client/check-token`,
          this.requestConfig()
        );

        if (response.data?.status !== false) {
          this.fillProfile(this.unwrap(response.data));
        }
      } catch (_) {
        this.profileHint =
          "Bạn có thể nhập hoặc chỉnh sửa thông tin người nhận bên dưới.";
      } finally {
        this.profileLoading = false;
      }
    },

    fillProfile(user) {
      this.form.TenNguoiNhan =
        user.HoTen || user.TenNguoiDung || user.Ten || this.form.TenNguoiNhan;

      this.form.SoDienThoaiNhan =
        user.SoDienThoai || user.SDT || this.form.SoDienThoaiNhan;

      this.form.DiaChiGiaoHang =
        user.DiaChi || user.DiaChiGiaoHang || this.form.DiaChiGiaoHang;
    },

    resetCheckoutState() {
      this.error = "";
      this.successMessage = "";
      this.voucherError = "";
      this.voucherCode = "";
      this.appliedVoucher = null;
      this.orderSuccess = null;
      this.cart = [];
    },

    async loadCheckoutItems() {
      this.error = "";
      this.cart = [];
      this.cartLoading = true;

      if (!this.getToken()) {
        this.error = "Bạn cần đăng nhập trước khi đặt hàng.";
        this.cartLoading = false;
        return;
      }

      try {
        if (this.isBuyNow) {
          await this.loadSingleProduct();
        } else {
          await this.loadFullCart();
        }
      } catch (err) {
        this.error =
          err?.response?.data?.message ||
          err.message ||
          "Không thể tải dữ liệu đặt hàng.";

        this.cart = [];
      } finally {
        this.cartLoading = false;
      }
    },

    // Đặt hàng từ giỏ: lấy toàn bộ các mặt hàng trong database.
    async loadFullCart() {
      let selectedIds;

      try {
        selectedIds = JSON.parse(
          localStorage.getItem("checkoutSelectedMaGH") || "null"
        );
      } catch {
        selectedIds = null;
      }

      if (!Array.isArray(selectedIds) || selectedIds.length === 0) {
        throw new Error("Chưa có sản phẩm được chọn. Hãy quay lại giỏ hàng.");
      }

      const selectedSet = new Set(selectedIds.map(String));

      const response = await axios.get(
        `${API}/gio-hang/get-data`,
        this.requestConfig()
      );

      if (response.data?.status === false) {
        throw new Error(response.data.message || "Không lấy được giỏ hàng.");
      }

      const rows = response.data?.data;

      if (!Array.isArray(rows)) {
        throw new Error("Dữ liệu giỏ hàng không hợp lệ.");
      }

      // Lọc theo MaGH đã được chọn
      const selectedRows = rows.filter((row) =>
        selectedSet.has(String(row.MaGH))
      );

      if (selectedRows.length !== selectedSet.size) {
        throw new Error(
          "Một số sản phẩm đã thay đổi hoặc bị xóa. Hãy quay lại giỏ hàng và chọn lại."
        );
      }

      this.cart = selectedRows.map((row) => {
        const product = row.san_pham || row.sanPham || {};
        const stock = product.ton_kho || product.tonKho || {};

        const maSP = row.MaSP ?? product.MaSP;

        const stockQty = Number(stock.SoLuongTon ?? product.SoLuongTon ?? 0);

        const heldQty = Number(
          stock.SoLuongDangGiu ?? product.SoLuongDangGiu ?? 0
        );

        return {
          key: String(row.MaGH),
          MaGH: row.MaGH,
          MaSP: Number(maSP),
          name: product.TenSP || row.TenSP || `Sản phẩm #${maSP}`,
          price: Number(product.GiaBan ?? row.GiaBan ?? 0),
          qty: Number(row.SoLuong ?? 1),
          image: product.HinhAnh || row.HinhAnh || "",
          soLuongKhaDung: Math.max(0, stockQty - heldQty),
          TrangThaiSP: product.TrangThaiSP,
        };
      });
    },

    // Mua riêng: chỉ tải đúng MaSP được truyền trong URL.
    async loadSingleProduct() {
      const productId = Number(
        this.$route?.query?.MaSP || this.$route?.query?.productId || 0
      );

      const quantity = Math.max(1, Number(this.$route?.query?.SoLuong || 1));

      if (!Number.isInteger(productId) || productId <= 0) {
        throw new Error(
          "Không xác định được sản phẩm. Hãy quay lại trang sản phẩm và bấm Mua ngay."
        );
      }

      const response = await axios.get(`${API}/san-pham/get-data`);

      if (response.data?.status === false) {
        throw new Error(response.data.message || "Không tải được sản phẩm.");
      }

      const products = Array.isArray(response.data?.data)
        ? response.data.data
        : [];

      const product = products.find((item) => Number(item.MaSP) === productId);

      if (!product) {
        throw new Error("Sản phẩm không tồn tại hoặc đã ngừng kinh doanh.");
      }

      if (product.TrangThaiSP && product.TrangThaiSP !== "DangBan") {
        throw new Error("Sản phẩm hiện không được phép kinh doanh.");
      }

      const stock = product.ton_kho || product.tonKho || {};
      const stockQty = Number(stock.SoLuongTon ?? product.SoLuongTon ?? 0);
      const heldQty = Number(
        stock.SoLuongDangGiu ?? product.SoLuongDangGiu ?? 0
      );
      const available = Math.max(0, stockQty - heldQty);

      if (available <= 0) {
        throw new Error("Sản phẩm hiện đã hết hàng.");
      }

      this.cart = [
        {
          key: String(product.MaSP),
          MaSP: Number(product.MaSP),
          name: product.TenSP || `Sản phẩm #${product.MaSP}`,
          price: Number(product.GiaBan || 0),
          qty: Math.min(quantity, available),
          image: product.HinhAnh || "",
          soLuongKhaDung: available,
          TrangThaiSP: product.TrangThaiSP,
        },
      ];
    },

    imageUrl(path) {
      if (!path) return "";
      if (/^https?:\/\//i.test(path)) return path;

      if (
        path.startsWith("/images/") ||
        path.startsWith("/img_") ||
        path.startsWith("/assets/")
      ) {
        return path;
      }

      if (path.startsWith("/storage/")) return `${API_ROOT}${path}`;

      return `${API_ROOT}/storage/${String(path)
        .replace(/^\/?storage\//, "")
        .replace(/^\/+/, "")}`;
    },

    markImageBroken(key) {
      if (!this.brokenImages.includes(key)) {
        this.brokenImages = [...this.brokenImages, key];
      }
    },

    changeSingleQty(delta) {
      const item = this.cart[0];

      if (!item || !this.isBuyNow) return;

      const nextQty = item.qty + delta;

      if (nextQty < 1 || nextQty > item.soLuongKhaDung) return;

      item.qty = nextQty;
      this.voucherError = "";

      // Số tiền ưu đãi sẽ được tính lại theo số lượng mới.
    },

    formatMoney(value) {
      return `${Number(value || 0).toLocaleString("vi-VN")} ₫`;
    },

    async applyVoucher() {
      if (this.voucherLoading || this.appliedVoucher) return;

      // Chuẩn hóa mã để tránh khác biệt do người dùng nhập chữ thường.
      const code = this.voucherCode.trim().toUpperCase();

      if (!code) {
        this.voucherError = "Vui lòng nhập mã voucher.";
        return;
      }

      if (!this.cart.length || this.subtotal <= 0) {
        this.voucherError =
          "Giỏ hàng chưa có sản phẩm hợp lệ để áp dụng voucher.";
        return;
      }

      this.voucherCode = code;
      this.voucherLoading = true;
      this.voucherError = "";
      this.error = "";

      try {
        /*
         * Endpoint tra cứu voucher công khai cần được thêm ở backend:
         * GET /api/voucher/kiem-tra?MaGiamGia=CODE
         *
         * Endpoint cần chỉ trả voucher đang hoạt động, chưa hết hạn.
         * Tuyệt đối không thay bằng danh sách voucher mẫu trên frontend.
         */
        const response = await axios.get(`${API}/voucher/kiem-tra`, {
          ...this.requestConfig(),
          params: { MaGiamGia: code },
        });

        if (response.data?.status === false) {
          throw new Error(response.data.message || "Voucher không hợp lệ.");
        }

        const voucher = response.data?.data;

        if (!voucher || !voucher.MaVoucher) {
          throw new Error("Dữ liệu voucher trả về không hợp lệ.");
        }

        const percent = Number(voucher.GiaTriGiam);
        if (!Number.isFinite(percent) || percent <= 0 || percent > 100) {
          throw new Error("Giá trị giảm của voucher không hợp lệ.");
        }

        if (voucher.TrangThai !== "DangSuDung") {
          throw new Error("Voucher hiện không còn được sử dụng.");
        }

        if (
          voucher.NgayHetHan &&
          new Date(`${voucher.NgayHetHan}T23:59:59`) < new Date()
        ) {
          throw new Error("Voucher đã hết hạn sử dụng.");
        }

        this.appliedVoucher = voucher;
        this.voucherCode = voucher.MaGiamGia || code;
        this.successMessage = `Đã áp dụng voucher ${this.voucherCode}.`;
      } catch (err) {
        this.appliedVoucher = null;

        if (err?.response?.status === 404) {
          this.voucherError =
            "API tra cứu voucher chưa được cấu hình. Cần bổ sung endpoint công khai ở backend.";
        } else {
          this.voucherError =
            err?.response?.data?.message ||
            err.message ||
            "Không thể kiểm tra voucher.";
        }
      } finally {
        this.voucherLoading = false;
      }
    },

    removeVoucher() {
      this.appliedVoucher = null;
      this.voucherCode = "";
      this.voucherError = "";
      this.successMessage = "";
    },

    validateForm() {
      this.error = "";

      if (!this.cart.length) {
        this.error = this.isBuyNow
          ? "Không có sản phẩm để đặt hàng."
          : "Giỏ hàng đang trống.";
        return false;
      }

      if (!this.form.TenNguoiNhan.trim()) {
        this.error = "Vui lòng nhập họ tên người nhận.";
        return false;
      }

      const phone = this.form.SoDienThoaiNhan.trim();

      if (!/^[0-9+().\s-]{9,15}$/.test(phone)) {
        this.error = "Vui lòng kiểm tra lại số điện thoại người nhận.";
        return false;
      }

      if (!this.form.DiaChiGiaoHang.trim()) {
        this.error = "Vui lòng nhập địa chỉ giao hàng.";
        return false;
      }

      if (
        this.paymentMethod === "ChuyenKhoan" &&
        (this.bankInfo.bankName === "Tên ngân hàng" ||
          this.bankInfo.accountName === "TÊN CHỦ TÀI KHOẢN" ||
          this.bankInfo.accountNumber === "SỐ TÀI KHOẢN")
      ) {
        this.error =
          "Bạn cần cấu hình thông tin tài khoản ngân hàng trước khi chọn chuyển khoản.";
        return false;
      }

      return true;
    },

    async placeOrder() {
      if (this.submitting || !this.validateForm()) return;

      const phone = this.form.SoDienThoaiNhan.replace(/\D/g, "");

      if (phone.length !== 10) {
        this.error = "Số điện thoại nhận phải có đúng 10 chữ số.";
        return;
      }

      this.submitting = true;
      this.error = "";
      this.successMessage = "";

      try {
        const payload = {
          PhuongThucThanhToan: this.paymentMethod,
          TenNguoiNhan: this.form.TenNguoiNhan.trim(),
          SoDienThoaiNhan: phone,
          DiaChiGiaoHang: this.form.DiaChiGiaoHang.trim(),
        };

        if (this.appliedVoucher) {
          payload.MaVoucher = this.appliedVoucher.MaVoucher;
        }

        if (this.isBuyNow) {
          // MUA RIÊNG TỪ TRANG CHI TIẾT
          const item = this.cart[0];

          if (!item) {
            throw new Error("Không tìm thấy sản phẩm cần mua.");
          }

          payload.MuaNgay = true;
          payload.MaSP = Number(item.MaSP);
          payload.SoLuong = Number(item.qty);
        } else {
          // MUA CÁC DÒNG ĐƯỢC CHỌN TỪ GIỎ
          let selectedIds;

          try {
            selectedIds = JSON.parse(
              localStorage.getItem("checkoutSelectedMaGH") || "null"
            );
          } catch {
            selectedIds = null;
          }

          if (!Array.isArray(selectedIds) || selectedIds.length === 0) {
            throw new Error(
              "Không tìm thấy sản phẩm được chọn. Hãy quay lại giỏ hàng."
            );
          }

          const visibleIds = new Set(
            this.cart.map((item) => String(item.MaGH))
          );

          const validIds = selectedIds.filter((id) =>
            visibleIds.has(String(id))
          );

          if (validIds.length !== selectedIds.length) {
            throw new Error(
              "Danh sách sản phẩm đã thay đổi. Hãy quay lại giỏ hàng và chọn lại."
            );
          }

          payload.MaGHs = validIds.map(Number);
        }

        const response = await axios.post(
          `${API}/don-hang/add-data`,
          payload,
          this.requestConfig()
        );

        if (response.data?.status === false) {
          throw new Error(response.data.message || "Đặt hàng chưa thành công.");
        }

        // Chỉ xóa trạng thái lựa chọn sau khi BE báo đặt hàng thành công.
        localStorage.removeItem("checkoutSelectedMaGH");

        // Chuyển sang đúng trang lịch sử mua hàng trong router của bạn.
        await this.$router.push("/lich-su-mua");
      } catch (err) {
        this.error =
          err?.response?.data?.message ||
          (err?.response?.data?.errors &&
            Object.values(err.response.data.errors).flat().join(" ")) ||
          err.message ||
          "Không thể tạo đơn hàng. Vui lòng thử lại.";
      } finally {
        this.submitting = false;
      }

      // Báo cho TopCL cập nhật số đơn đang chờ xác nhận
      window.dispatchEvent(
        new Event("techshop:orders-updated")
      );
    },

    async copyTransferContent() {
      try {
        await navigator.clipboard.writeText(this.transferContent);
        this.copied = true;
      } catch (_) {
        this.error = `Nội dung chuyển khoản: ${this.transferContent}`;
      }
    },

    goBack() {
      if (window.history.length > 1) {
        this.$router.back();
        return;
      }

      this.$router.push(this.isBuyNow ? "/chi-tiet-sp" : "/gio-hang");
    },

    goHome() {
      this.$router.push("/trang-chu");
    },

    goHistory() {
      const candidates = [
        "/lich-su-mua-hang",
        "/client/lich-su-mua-hang",
        "/don-hang",
      ];

      const target = candidates.find(
        (path) => this.$router.resolve(path).matched.length > 0
      );

      this.$router.push(target || "/trang-chu");
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Be+Vietnam+Pro:wght@400;500;600;700;800;900&family=Manrope:wght@500;600;700;800&display=swap");

:global(html) { scroll-behavior: smooth; }
:global(body) { margin: 0; background: #f4f6fc; }

/* =========================================================
   BIẾN MÀU (đồng bộ với các trang khác)
   ========================================================= */
.checkout-page {
  --ink: #0f172a;
  --text: #334155;
  --muted: #64748b;
  --line: #e2e8f0;
  --soft: #f1f5f9;
  --primary: #4f46e5;
  --blue: #2563eb;
  --accent: #06b6d4;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --grad-hero: linear-gradient(135deg, #1e1b4b 0%, #312e81 45%, #1d4ed8 100%);
  --grad-hot: linear-gradient(135deg, #fb7185 0%, #f43f5e 50%, #f97316 100%);
  --grad-success: linear-gradient(135deg, #34d399 0%, #059669 100%);
  --shadow-sm: 0 2px 10px rgba(15, 23, 42, 0.06);
  --shadow-md: 0 10px 30px rgba(15, 23, 42, 0.08);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  position: relative;
  isolation: isolate;
  min-height: 100vh;
  overflow: hidden;
  padding: 0 26px 36px;
  color: var(--ink);
  background:
    radial-gradient(900px 480px at 100% -5%, rgba(99, 102, 241, 0.13), transparent 60%),
    radial-gradient(700px 480px at -5% 35%, rgba(6, 182, 212, 0.1), transparent 60%),
    #f4f6fc;
  font-family: "Be Vietnam Pro", "Roboto", "Inter", Arial, sans-serif;
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(18px); }
  to { opacity: 1; transform: translateY(0); }
}
@keyframes spin { to { transform: rotate(360deg); } }
@keyframes floatBlob {
  0%, 100% { transform: translate(0, 0) scale(1); }
  50% { transform: translate(24px, -18px) scale(1.08); }
}
@keyframes pulseDot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.35); }
}
@keyframes pulseRing {
  0% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.5); }
  70% { box-shadow: 0 0 0 16px rgba(16, 185, 129, 0); }
  100% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); }
}
@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}
@keyframes popIn {
  from { opacity: 0; transform: scale(0.92) translateY(14px); }
  to { opacity: 1; transform: scale(1) translateY(0); }
}

/* =========================================================
   NỀN TRANG TRÍ
   ========================================================= */
.ambient {
  position: absolute;
  z-index: -1;
  pointer-events: none;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.35;
  animation: floatBlob 14s ease-in-out infinite;
}
.ambient-one { top: -200px; right: -160px; width: 520px; height: 520px; background: #a5b4fc; }
.ambient-two { top: 520px; left: -240px; width: 460px; height: 460px; background: #67e8f9; animation-delay: -6s; }

.checkout-shell { position: relative; width: min(1260px, 100%); margin: 0 auto; }

/* =========================================================
   HEADER CỬA HÀNG
   ========================================================= */
.shop-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  min-height: 84px;
  border-bottom: 1px solid rgba(226, 232, 240, 0.9);
}

.brand { display: flex; align-items: center; gap: 12px; padding: 0; border: 0; color: inherit; background: transparent; text-align: left; cursor: pointer; }
.brand-mark {
  display: grid;
  place-items: center;
  width: 46px;
  height: 46px;
  flex: 0 0 46px;
  border-radius: 15px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 10px 22px rgba(79, 70, 229, 0.4);
  font-family: Manrope, sans-serif;
  font-size: 27px;
  font-weight: 800;
  letter-spacing: -2px;
  transition: transform 0.35s var(--ease);
}
.brand:hover .brand-mark { transform: rotate(-8deg) scale(1.08); }
.brand-mark span { color: #fde68a; }
.brand-copy strong, .brand-copy small { display: block; }
.brand-copy strong {
  font-family: Manrope, sans-serif;
  font-size: 16px;
  font-weight: 800;
  letter-spacing: 1.2px;
  background: linear-gradient(90deg, #0f172a, #4f46e5);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.brand-copy small { margin-top: 4px; color: #94a3b8; font-size: 9px; font-weight: 800; letter-spacing: 1.8px; }

.header-trust {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 8px 15px 8px 9px;
  border: 1px solid #d1fae5;
  border-radius: 999px;
  background: #f0fdf7;
}
.trust-icon {
  display: grid;
  place-items: center;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-success);
  font-size: 13px;
  font-weight: 800;
}
.header-trust strong, .header-trust small { display: block; }
.header-trust strong { color: #065f46; font-size: 11.5px; font-weight: 800; }
.header-trust small { margin-top: 3px; color: #5b8a77; font-size: 10px; }

.back-button {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 8px 0 8px 12px;
  border: 0;
  color: var(--muted);
  background: transparent;
  font: inherit;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
  transition: color 0.2s;
}
.back-button:hover { color: var(--primary); }
.back-button span {
  display: grid;
  place-items: center;
  width: 32px;
  height: 32px;
  border: 1.5px solid var(--line);
  border-radius: 50%;
  color: #475569;
  background: #fff;
  font-size: 16px;
  transition: all 0.28s var(--ease);
}
.back-button:hover span { color: #fff; border-color: transparent; background: var(--grad-primary); transform: translateX(-4px); }

/* =========================================================
   BANNER GIỚI THIỆU
   ========================================================= */
.page-intro {
  position: relative;
  overflow: hidden;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 24px;
  margin: 26px 0 22px;
  padding: 34px 36px;
  border-radius: 26px;
  color: #fff;
  background: var(--grad-hero);
  box-shadow: 0 22px 50px rgba(49, 46, 129, 0.33);
  animation: fadeUp 0.6s var(--ease) both;
}
.page-intro::before,
.page-intro::after { content: ""; position: absolute; border-radius: 50%; pointer-events: none; }
.page-intro::before { top: -90px; right: -60px; width: 300px; height: 300px; background: radial-gradient(circle, rgba(34, 211, 238, 0.45), transparent 68%); }
.page-intro::after { bottom: -120px; left: 30%; width: 280px; height: 280px; background: radial-gradient(circle, rgba(167, 139, 250, 0.4), transparent 68%); }
.page-intro > * { position: relative; z-index: 1; }

.eyebrow {
  display: flex;
  align-items: center;
  gap: 9px;
  margin: 0 0 13px;
  color: var(--primary);
  font-size: 10.5px;
  font-weight: 800;
  letter-spacing: 1.8px;
}
.eyebrow-line { width: 28px; height: 3px; border-radius: 3px; background: linear-gradient(90deg, #22d3ee, #a78bfa); }
.page-intro .eyebrow { color: #c7d2fe; }

.page-intro h1 {
  margin: 0;
  color: #fff;
  font-family: Manrope, "Be Vietnam Pro", sans-serif;
  font-size: clamp(30px, 4vw, 46px);
  font-weight: 800;
  letter-spacing: -1.6px;
  line-height: 1.15;
}
.page-intro p:not(.eyebrow) { margin: 12px 0 0; color: #c7d2fe; font-size: 13.5px; line-height: 1.75; }

.purchase-mode {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 11px 16px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 999px;
  color: #fff;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  font-size: 10.5px;
  font-weight: 800;
  letter-spacing: 0.8px;
  white-space: nowrap;
}
.purchase-mode.single { background: rgba(52, 211, 153, 0.25); border-color: rgba(110, 231, 183, 0.5); }
.mode-dot { width: 8px; height: 8px; border-radius: 50%; background: #67e8f9; animation: pulseDot 1.8s ease-in-out infinite; }
.purchase-mode.single .mode-dot { background: #6ee7b7; }

/* =========================================================
   CÁC BƯỚC
   ========================================================= */
.steps {
  display: flex;
  align-items: center;
  gap: 18px;
  margin-bottom: 24px;
  padding: 18px 24px;
  border: 1px solid var(--line);
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.92);
  box-shadow: var(--shadow-md);
  animation: fadeUp 0.6s var(--ease) 0.08s both;
}
.step { display: flex; align-items: center; gap: 11px; min-width: 0; color: #94a3b8; }
.step-number {
  display: grid;
  place-items: center;
  width: 38px;
  height: 38px;
  flex: 0 0 38px;
  border: 2px solid var(--line);
  border-radius: 50%;
  color: #94a3b8;
  background: #fff;
  font-size: 11px;
  font-weight: 800;
}
.step.is-active .step-number {
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35);
}
.step strong, .step small { display: block; white-space: nowrap; }
.step strong { color: var(--ink); font-size: 12.5px; font-weight: 800; }
.step small { margin-top: 4px; color: #94a3b8; font-size: 10.5px; }
.step-line { flex: 1; min-width: 12px; height: 3px; border-radius: 3px; background: linear-gradient(90deg, #a5b4fc, #e2e8f0); }

/* =========================================================
   BỐ CỤC
   ========================================================= */
.checkout-layout { display: grid; grid-template-columns: minmax(0, 1fr) 420px; align-items: start; gap: 24px; }
.checkout-main { display: grid; gap: 20px; min-width: 0; }

.surface-card,
.summary-card,
.support-card {
  border: 1px solid var(--line);
  border-radius: 22px;
  background: #fff;
  box-shadow: var(--shadow-md);
}
.surface-card {
  position: relative;
  margin-bottom: 20px;
  padding: 28px;
  animation: fadeUp 0.6s var(--ease) both;
  transition: box-shadow 0.3s var(--ease), border-color 0.3s;
}
.surface-card:hover { border-color: #c7d2fe; box-shadow: 0 16px 38px rgba(49, 46, 129, 0.1); }

.section-heading { display: flex; align-items: flex-start; gap: 14px; margin-bottom: 26px; }
.section-number {
  display: grid;
  place-items: center;
  width: 44px;
  height: 44px;
  flex: 0 0 44px;
  border-radius: 14px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35);
  font-family: Manrope, sans-serif;
  font-size: 12.5px;
  font-weight: 800;
  transition: transform 0.35s var(--ease);
}
.surface-card:hover .section-number { transform: rotate(-8deg) scale(1.08); }
.section-heading-copy { flex: 1; min-width: 0; }
.section-heading-copy h2 { margin: 2px 0 6px; color: var(--ink); font-size: 18px; font-weight: 800; letter-spacing: -0.3px; }
.section-heading-copy p { margin: 0; color: var(--muted); font-size: 12px; line-height: 1.65; }
.required-note {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 11px;
  border-radius: 999px;
  color: #be123c;
  background: #fff1f2;
  font-size: 10.5px;
  font-weight: 700;
  white-space: nowrap;
}
.required-note i { width: 6px; height: 6px; border-radius: 50%; background: #f43f5e; }

/* =========================================================
   FORM
   ========================================================= */
.fields-grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 20px 16px; }
.field { display: flex; flex-direction: column; gap: 9px; min-width: 0; }
.field-full { grid-column: 1 / -1; }
.field > span { color: #1e293b; font-size: 12.5px; font-weight: 800; }
.field b { color: #f43f5e; }

.field input,
.field textarea,
.voucher-input-wrap input {
  display: block;
  width: 100%;
  min-width: 0;
  padding: 13px 15px;
  border: 1.5px solid var(--line);
  border-radius: 13px;
  outline: none;
  color: #1e293b;
  background: #f8fafc;
  font: inherit;
  font-size: 13px;
  line-height: 1.6;
  transition: all 0.25s var(--ease);
}
.field input { height: 50px; }
.field textarea { min-height: 106px; resize: vertical; }
.field input:hover, .field textarea:hover { border-color: #c7d2fe; }
.field input::placeholder, .field textarea::placeholder, .voucher-input-wrap input::placeholder { color: #a0aabc; }
.field input:focus, .field textarea:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}
.field small { margin-top: -3px; color: #94a3b8; font-size: 11px; line-height: 1.6; }

.privacy-note,
.payment-explainer {
  display: flex;
  align-items: flex-start;
  gap: 11px;
  margin-top: 22px;
  padding: 13px 15px;
  border: 1px solid #e0e7ff;
  border-radius: 14px;
  color: #475569;
  background: linear-gradient(180deg, #eef2ff, #f8faff);
  font-size: 11.5px;
  line-height: 1.7;
}
.privacy-note > span,
.explain-icon {
  display: grid;
  place-items: center;
  width: 24px;
  height: 24px;
  flex: 0 0 24px;
  border-radius: 8px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 12px;
  font-weight: 800;
}
.privacy-note p, .payment-explainer p { margin: 0; }
.inline-loading { display: flex; align-items: center; gap: 9px; margin: -5px 0 18px; color: var(--muted); font-size: 11.5px; }
.inline-hint { margin: -8px 0 16px; padding: 11px 14px; border-radius: 12px; color: #92400e; background: #fffbeb; border: 1px solid #fde68a; font-size: 11.5px; line-height: 1.6; }

/* =========================================================
   VOUCHER
   ========================================================= */
.voucher-icon {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  margin-left: auto;
  border-radius: 13px;
  color: #fff;
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38);
  font-size: 19px;
  font-weight: 800;
}
.voucher-entry { display: grid; gap: 12px; }
.voucher-input-wrap {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 6px;
  border: 2px dashed #fcd34d;
  border-radius: 16px;
  background: linear-gradient(120deg, #fffbeb, #fff);
  transition: 0.25s var(--ease);
}
.voucher-input-wrap:focus-within { border-color: #f59e0b; box-shadow: 0 0 0 4px rgba(245, 158, 11, 0.14); }
.ticket-icon {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  flex: 0 0 42px;
  border-radius: 12px;
  color: #b45309;
  background: #fef3c7;
  font-size: 19px;
}
.voucher-input-wrap input {
  height: 44px;
  padding: 8px 4px;
  border: 0;
  background: transparent;
  box-shadow: none;
  font-weight: 800;
  letter-spacing: 1px;
  text-transform: uppercase;
}
.voucher-input-wrap input:focus { border: 0; background: transparent; box-shadow: none; }

.voucher-apply,
.voucher-remove {
  align-self: stretch;
  padding: 0 20px;
  border: 0;
  border-radius: 11px;
  font: inherit;
  font-size: 12px;
  font-weight: 800;
  white-space: nowrap;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.voucher-apply { color: #fff; background: var(--grad-primary); box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3); }
.voucher-apply:hover:not(:disabled) { transform: translateY(-2px); box-shadow: 0 10px 20px rgba(79, 70, 229, 0.42); }
.voucher-apply:disabled { cursor: not-allowed; opacity: 0.5; box-shadow: none; }
.voucher-remove { color: #be123c; background: #ffe4e6; }
.voucher-remove:hover { background: #fecdd3; }

.voucher-help, .voucher-message { margin: 0; color: var(--muted); font-size: 11.5px; line-height: 1.65; }
.voucher-error { padding: 10px 13px; border-radius: 12px; color: #be123c; background: #fff1f2; font-weight: 600; }

.voucher-applied {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px;
  border: 1px solid #a7f3d0;
  border-radius: 16px;
  background: linear-gradient(120deg, #ecfdf5, #f8fffb);
  animation: popIn 0.4s var(--ease) both;
}
.voucher-applied-check {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  flex: 0 0 30px;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-success);
  box-shadow: 0 6px 12px rgba(16, 185, 129, 0.35);
  font-weight: 800;
}
.voucher-applied > div { flex: 1; min-width: 0; }
.voucher-applied strong { color: #065f46; font-size: 13px; font-weight: 800; letter-spacing: 0.5px; }
.voucher-applied small { display: block; margin-top: 4px; color: #5b8a77; font-size: 11px; line-height: 1.5; }
.voucher-saving { font-size: 15px !important; white-space: nowrap; }

/* =========================================================
   THANH TOÁN
   ========================================================= */
.payment-options { display: grid; gap: 12px; }
.payment-option {
  position: relative;
  display: flex;
  align-items: center;
  gap: 14px;
  width: 100%;
  padding: 16px 18px;
  border: 2px solid var(--line);
  border-radius: 18px;
  color: inherit;
  background: #fff;
  text-align: left;
  cursor: pointer;
  transition: all 0.28s var(--ease);
}
.payment-option:hover { border-color: #c7d2fe; background: #fafbff; transform: translateY(-2px); box-shadow: 0 10px 22px rgba(79, 70, 229, 0.1); }
.payment-option.selected {
  border-color: transparent;
  background:
    linear-gradient(#f5f7ff, #fff) padding-box,
    var(--grad-primary) border-box;
  box-shadow: 0 12px 28px rgba(79, 70, 229, 0.22);
}
.payment-icon {
  display: grid;
  place-items: center;
  width: 50px;
  height: 50px;
  flex: 0 0 50px;
  border-radius: 15px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.3);
  font-size: 23px;
  font-weight: 800;
}
.bank-icon { background: var(--grad-success); box-shadow: 0 8px 16px rgba(16, 185, 129, 0.33); }
.payment-copy { flex: 1; min-width: 0; }
.payment-copy strong, .payment-copy small { display: block; }
.payment-copy strong { color: var(--ink); font-size: 13.5px; font-weight: 800; }
.payment-copy small { margin-top: 6px; color: var(--muted); font-size: 11.5px; line-height: 1.5; }
.radio-mark {
  display: grid;
  place-items: center;
  width: 24px;
  height: 24px;
  flex: 0 0 24px;
  border: 2px solid #cbd5e1;
  border-radius: 50%;
  background: #fff;
  transition: 0.25s;
}
.payment-option.selected .radio-mark { border-color: var(--primary); }
.radio-mark i { width: 12px; height: 12px; border-radius: 50%; background: var(--grad-primary); animation: popIn 0.3s var(--ease) both; }

.payment-explainer { margin-top: 16px; }
.payment-explainer strong { color: var(--primary); }
.payment-explainer .explain-icon { background: var(--grad-success); }
.qr-panel .payment-explainer .explain-icon { background: var(--grad-primary); }

.qr-panel {
  margin-top: 18px;
  padding: 20px;
  border: 1px solid #d1fae5;
  border-radius: 20px;
  background: linear-gradient(145deg, #f0fdf7, #fff);
  animation: popIn 0.4s var(--ease) both;
}
.qr-panel-heading,
.bank-heading { display: flex; justify-content: space-between; align-items: flex-start; gap: 12px; margin-bottom: 18px; }
.bank-eyebrow { color: #047857; font-size: 9.5px; font-weight: 800; letter-spacing: 1.4px; }
.qr-panel-heading h3,
.bank-heading h3 { margin: 7px 0 5px; color: var(--ink); font-size: 16px; font-weight: 800; }
.qr-panel-heading p { max-width: 400px; margin: 0; color: var(--muted); font-size: 11.5px; line-height: 1.6; }
.bank-label {
  padding: 7px 12px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-success);
  box-shadow: 0 6px 12px rgba(16, 185, 129, 0.3);
  font-size: 9.5px;
  font-weight: 800;
  letter-spacing: 0.6px;
  white-space: nowrap;
}

.bank-layout { display: grid; grid-template-columns: 170px minmax(0, 1fr); align-items: center; gap: 22px; }
.qr-frame {
  display: grid;
  place-items: center;
  width: 100%;
  max-width: 170px;
  aspect-ratio: 1;
  padding: 10px;
  border: 2px solid #fff;
  border-radius: 20px;
  background: #fff;
  box-shadow: 0 0 0 2px #a7f3d0, 0 14px 28px rgba(16, 185, 129, 0.2);
}
.qr-frame img { display: block; width: 100%; height: 100%; object-fit: contain; border-radius: 10px; }
.qr-missing { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 8px; padding: 12px; color: var(--muted); text-align: center; }
.qr-missing > span { color: #a5b4fc; font-size: 34px; }
.qr-missing strong { color: #475569; font-size: 11.5px; }
.qr-missing small { font-size: 10.5px; line-height: 1.6; }

.bank-information { display: grid; gap: 12px; min-width: 0; }
.bank-information > div > span,
.bank-information > div > strong { display: block; overflow-wrap: anywhere; }
.bank-information > div > span { margin-bottom: 4px; color: #94a3b8; font-size: 9.5px; font-weight: 700; letter-spacing: 0.7px; text-transform: uppercase; }
.bank-information > div > strong { color: var(--ink); font-size: 12.5px; font-weight: 800; }
.bank-information .bank-amount {
  font-size: 20px;
  font-weight: 900;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.bank-information .transfer-code { color: var(--primary); letter-spacing: 0.5px; }
.bank-warning { margin: 0; padding: 9px 12px; border-radius: 10px; color: #92400e; background: #fffbeb; font-size: 10.5px; line-height: 1.6; }

/* =========================================================
   TÓM TẮT ĐƠN
   ========================================================= */
.checkout-aside { position: sticky; top: 18px; min-width: 0; animation: fadeUp 0.6s var(--ease) 0.15s both; }
.summary-card { position: relative; overflow: hidden; padding: 24px; }
.summary-card::before { content: ""; position: absolute; inset: 0 0 auto 0; height: 5px; background: var(--grad-primary); }

.summary-heading { display: flex; align-items: flex-start; justify-content: space-between; gap: 12px; margin-bottom: 22px; }
.summary-eyebrow {
  display: inline-flex;
  padding: 4px 11px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 9.5px;
  font-weight: 800;
  letter-spacing: 1.4px;
}
.summary-heading h2 { margin: 10px 0 6px; color: var(--ink); font-size: 19px; font-weight: 800; letter-spacing: -0.3px; }
.summary-heading p { margin: 0; color: var(--muted); font-size: 11.5px; line-height: 1.6; }
.item-count {
  padding: 8px 13px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 15px;
  font-weight: 800;
  white-space: nowrap;
}
.item-count small { font-size: 10px; font-weight: 700; opacity: 0.9; }

.summary-items { display: grid; gap: 14px; }
.summary-item {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 0;
  padding: 10px;
  border: 1px solid #eef2f7;
  border-radius: 16px;
  background: #f8fafc;
  transition: 0.28s var(--ease);
}
.summary-item:hover { background: #fff; border-color: #c7d2fe; transform: translateX(4px); box-shadow: 0 8px 20px rgba(79, 70, 229, 0.1); }

.product-thumb {
  position: relative;
  display: grid;
  place-items: center;
  width: 64px;
  height: 64px;
  flex: 0 0 64px;
  border: 2px solid #fff;
  border-radius: 16px;
  background: #fff;
  box-shadow: 0 0 0 2px #e0e7ff, 0 6px 14px rgba(79, 70, 229, 0.12);
}
.product-thumb img { width: 100%; height: 100%; padding: 5px; object-fit: contain; border-radius: inherit; }
.image-fallback { color: var(--primary); font-size: 16px; font-weight: 800; }
.quantity-badge {
  position: absolute;
  top: -8px;
  right: -8px;
  display: grid;
  place-items: center;
  min-width: 23px;
  height: 23px;
  padding: 0 6px;
  border: 2px solid #fff;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-hot);
  box-shadow: 0 4px 10px rgba(244, 63, 94, 0.4);
  font-size: 10.5px;
  font-weight: 800;
}
.product-copy { flex: 1; min-width: 0; }
.product-copy > strong {
  display: -webkit-box;
  overflow: hidden;
  color: var(--ink);
  font-size: 12px;
  font-weight: 800;
  line-height: 1.55;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
.product-copy > small { display: block; overflow: hidden; margin-top: 5px; color: var(--muted); font-size: 10.5px; white-space: nowrap; text-overflow: ellipsis; }
.line-total { color: var(--ink); font-size: 12.5px; font-weight: 800; white-space: nowrap; }

.quantity-control {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  margin-top: 8px;
  padding: 3px;
  border: 1.5px solid var(--line);
  border-radius: 11px;
  background: #fff;
}
.quantity-control button {
  display: grid;
  place-items: center;
  width: 25px;
  height: 25px;
  border: 0;
  border-radius: 8px;
  color: var(--primary);
  background: #eef2ff;
  font-size: 15px;
  cursor: pointer;
  transition: 0.2s;
}
.quantity-control button:hover:not(:disabled) { color: #fff; background: var(--primary); }
.quantity-control button:disabled { opacity: 0.35; cursor: not-allowed; }
.quantity-control span { min-width: 14px; color: var(--ink); text-align: center; font-size: 12px; font-weight: 800; }

.price-summary { margin-top: 22px; padding-top: 19px; border-top: 1px dashed #cbd5e1; }
.price-line { display: flex; justify-content: space-between; align-items: flex-start; gap: 12px; margin-bottom: 14px; color: var(--muted); font-size: 12px; }
.price-line strong { color: var(--ink); font-size: 12.5px; font-weight: 800; white-space: nowrap; }
.free-shipping { padding: 3px 10px; border-radius: 999px; color: #047857; background: #d1fae5; font-size: 11px; font-weight: 700; text-align: right; }
.muted-value { color: #a0aabc; }
.discount-text { color: #047857 !important; }
.summary-divider { height: 1px; margin: 4px 0 18px; background: var(--line); }

.grand-total {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 13px;
  padding: 16px;
  border-radius: 18px;
  background: linear-gradient(135deg, #eef2ff, #e0f2fe);
}
.grand-total > div > span { display: block; color: #334155; font-size: 12.5px; font-weight: 800; }
.grand-total small { display: block; margin-top: 6px; color: var(--muted); font-size: 10.5px; line-height: 1.5; }
.grand-total-prices { display: flex; flex-direction: column; align-items: flex-end; gap: 4px; white-space: nowrap; }
.grand-total-prices del { color: #94a3b8; font-size: 12px; text-decoration: line-through; }
.grand-total-prices strong,
.grand-total > strong {
  font-size: 25px;
  font-weight: 900;
  letter-spacing: -0.8px;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.server-note { margin: 11px 0 0; color: #94a3b8; font-size: 10.5px; line-height: 1.65; }

.place-order-button {
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  width: 100%;
  min-height: 56px;
  margin-top: 22px;
  padding: 13px 18px;
  border: 0;
  border-radius: 16px;
  color: #fff;
  background: var(--grad-hot);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 12px 26px rgba(244, 63, 94, 0.38);
  font: inherit;
  font-size: 14px;
  font-weight: 800;
  letter-spacing: 0.4px;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease), background-position 0.4s;
}
.place-order-button::after {
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
.place-order-button:hover:not(:disabled) { transform: translateY(-3px); background-position: 100% 50%; box-shadow: 0 18px 34px rgba(244, 63, 94, 0.48); }
.place-order-button:hover:not(:disabled)::after { animation: shine 0.8s var(--ease); }
.place-order-button:active:not(:disabled) { transform: scale(0.98); }
.place-order-button:disabled { cursor: not-allowed; opacity: 0.6; box-shadow: none; }
.submit-arrow { margin-left: auto; font-size: 20px; transition: transform 0.25s var(--ease); }
.place-order-button:hover .submit-arrow { transform: translateX(5px); }

.order-note { display: flex; align-items: flex-start; justify-content: center; gap: 7px; margin-top: 14px; color: var(--muted); font-size: 10.5px; line-height: 1.6; text-align: center; }
.order-note span { color: #059669; font-weight: 900; }

.support-card { display: flex; align-items: flex-start; gap: 12px; margin-top: 16px; padding: 16px; }
.support-mark {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  flex: 0 0 30px;
  border-radius: 10px;
  color: #fff;
  background: var(--grad-primary);
  font-size: 13px;
  font-weight: 800;
}
.support-card strong { color: var(--ink); font-size: 12px; font-weight: 800; }
.support-card p { margin: 5px 0 0; color: var(--muted); font-size: 11px; line-height: 1.6; }

.summary-state, .empty-cart { padding: 28px 12px; color: var(--muted); text-align: center; font-size: 12px; }
.empty-cart-icon {
  display: inline-grid;
  place-items: center;
  width: 70px;
  height: 70px;
  margin-bottom: 12px;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 30px;
}
.empty-cart strong { display: block; color: var(--ink); font-size: 14px; font-weight: 800; }
.empty-cart p { margin: 7px 0 15px; line-height: 1.6; }
.full-button { width: 100%; }

/* =========================================================
   THÔNG BÁO
   ========================================================= */
.notice {
  display: flex;
  align-items: flex-start;
  gap: 11px;
  margin-bottom: 18px;
  padding: 14px 16px;
  border: 1px solid;
  border-radius: 16px;
  font-size: 12.5px;
  font-weight: 600;
  line-height: 1.6;
  animation: popIn 0.35s var(--ease) both;
}
.notice p { flex: 1; margin: 0; }
.notice > button { margin-left: auto; border: 0; color: inherit; background: transparent; font-size: 20px; cursor: pointer; }
.notice-symbol { display: grid; place-items: center; flex: 0 0 22px; width: 22px; height: 22px; border-radius: 50%; color: #fff; font-size: 12px; font-weight: 800; }
.notice-error { border-color: #fecdd3; color: #be123c; background: linear-gradient(180deg, #fff1f2, #fff); }
.notice-error .notice-symbol { background: var(--grad-hot); }
.notice-success { border-color: #a7f3d0; color: #047857; background: linear-gradient(180deg, #ecfdf5, #fff); }
.notice-success .notice-symbol { background: var(--grad-success); }

/* =========================================================
   MÀN HÌNH THÀNH CÔNG
   ========================================================= */
.success-screen {
  position: relative;
  overflow: hidden;
  max-width: 800px;
  margin: 36px auto;
  padding: 40px;
  border: 1px solid var(--line);
  border-radius: 28px;
  background: #fff;
  box-shadow: 0 24px 60px rgba(15, 23, 42, 0.12);
  animation: popIn 0.55s var(--ease) both;
}
.success-screen::before { content: ""; position: absolute; inset: 0 0 auto 0; height: 6px; background: var(--grad-success); }
.success-visual {
  display: grid;
  place-items: center;
  width: 80px;
  height: 80px;
  margin: 0 auto 22px;
  border-radius: 50%;
  background: #d1fae5;
  animation: pulseRing 2s ease-out infinite;
}
.success-check {
  display: grid;
  place-items: center;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-success);
  box-shadow: 0 10px 22px rgba(16, 185, 129, 0.45);
  font-size: 28px;
  font-weight: 800;
}
.success-eyebrow { justify-content: center; color: #047857; }
.success-screen > h2 {
  margin: 0;
  font-family: Manrope, sans-serif;
  font-size: 32px;
  font-weight: 800;
  letter-spacing: -1px;
  text-align: center;
  background: linear-gradient(90deg, #0f172a, #4f46e5);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.success-description { max-width: 520px; margin: 12px auto 24px; color: var(--muted); font-size: 13px; line-height: 1.8; text-align: center; }

.success-order-number {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 18px;
  border: 1px solid #c7d2fe;
  border-radius: 18px;
  background: linear-gradient(120deg, #eef2ff, #f0f9ff);
}
.success-order-number > span:first-child { color: #94a3b8; font-size: 9.5px; font-weight: 800; letter-spacing: 1.2px; }
.success-order-number > strong {
  flex: 1;
  font-size: 22px;
  font-weight: 900;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.pending-pill { display: inline-flex; align-items: center; gap: 7px; padding: 7px 12px; border-radius: 999px; color: #b45309; background: #fef3c7; font-size: 10.5px; font-weight: 800; }
.pending-pill i { width: 7px; height: 7px; border-radius: 50%; background: #f59e0b; animation: pulseDot 1.6s ease-in-out infinite; }

.success-details { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 12px; margin-top: 18px; }
.success-details > div { padding: 14px 16px; border: 1px solid #eef2f7; border-radius: 16px; background: #f8fafc; }
.success-details > div > span,
.success-details > div > strong { display: block; overflow-wrap: anywhere; }
.success-details > div > span { margin-bottom: 6px; color: var(--muted); font-size: 10.5px; font-weight: 600; }
.success-details > div > strong { color: var(--ink); font-size: 12.5px; font-weight: 800; }
.success-details .detail-wide { grid-column: 1 / -1; }
.success-details .success-total { font-size: 20px; font-weight: 900; background: var(--grad-primary); -webkit-background-clip: text; background-clip: text; -webkit-text-fill-color: transparent; }

.success-bank { margin-top: 20px; padding: 22px; border: 1px solid #d1fae5; border-radius: 22px; background: linear-gradient(145deg, #f0fdf7, #fff); }
.bank-heading { margin-bottom: 20px; }
.bank-heading h3 { margin-bottom: 0; }

.copy-button {
  padding: 10px 14px;
  border: 1.5px solid #c7d2fe;
  border-radius: 11px;
  color: var(--primary);
  background: #fff;
  font: inherit;
  font-size: 11px;
  font-weight: 800;
  cursor: pointer;
  transition: 0.25s var(--ease);
}
.copy-button:hover { color: #fff; border-color: transparent; background: var(--grad-primary); transform: translateY(-2px); box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35); }

.success-bank .bank-warning { margin-top: 16px; }
.success-actions { display: flex; justify-content: center; flex-wrap: wrap; gap: 11px; margin-top: 26px; }

.button-primary,
.button-secondary {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  min-height: 48px;
  padding: 12px 22px;
  border: 1.5px solid transparent;
  border-radius: 14px;
  font: inherit;
  font-size: 12.5px;
  font-weight: 800;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease), background 0.25s, border-color 0.25s, color 0.25s;
}
.button-primary { color: #fff; background: var(--grad-primary); box-shadow: 0 10px 22px rgba(79, 70, 229, 0.35); }
.button-primary:hover { transform: translateY(-3px); box-shadow: 0 16px 30px rgba(79, 70, 229, 0.45); }
.button-primary span { font-size: 17px; transition: transform 0.25s var(--ease); }
.button-primary:hover span { transform: translateX(5px); }
.button-secondary { border-color: var(--line); color: #475569; background: #fff; }
.button-secondary:hover { color: var(--primary); border-color: #c7d2fe; background: #eef2ff; transform: translateY(-2px); }

.spinner {
  display: inline-block;
  width: 17px;
  height: 17px;
  border: 2.5px solid #e0e7ff;
  border-top-color: var(--primary);
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
.spinner-light { border-color: rgba(255, 255, 255, 0.35); border-top-color: #fff; }

/* =========================================================
   FOOTER
   ========================================================= */
.page-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 14px;
  margin-top: 32px;
  padding-top: 20px;
  border-top: 1px solid var(--line);
  color: #94a3b8;
  font-size: 11px;
}
.page-footer strong {
  font-family: Manrope, sans-serif;
  font-size: 13px;
  letter-spacing: 1.5px;
  background: var(--grad-primary);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
.page-footer span:last-child { color: #64748b; font-size: 9.5px; font-weight: 800; letter-spacing: 0.8px; }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1020px) {
  .checkout-layout { grid-template-columns: minmax(0, 1fr) 360px; gap: 16px; }
  .surface-card { padding: 22px; }
  .summary-card { padding: 20px; }
  .header-trust { display: none; }
}

@media (max-width: 780px) {
  .checkout-page { padding: 0 14px 26px; }
  .checkout-layout { grid-template-columns: minmax(0, 1fr); }
  .checkout-aside { position: static; }
  .page-intro { align-items: flex-start; flex-direction: column; padding: 26px 22px; border-radius: 22px; }
  .steps { gap: 9px; padding: 14px; }
  .step { gap: 7px; }
  .step-number { width: 32px; height: 32px; flex-basis: 32px; }
  .step strong { font-size: 10.5px; }
  .step small { display: none; }
  .step-line { min-width: 7px; }
  .bank-layout { grid-template-columns: 140px minmax(0, 1fr); gap: 15px; }
  .qr-frame { max-width: 140px; }
}

@media (max-width: 520px) {
  .shop-header { min-height: 72px; gap: 10px; }
  .brand-mark { width: 40px; height: 40px; flex-basis: 40px; border-radius: 13px; font-size: 23px; }
  .brand-copy strong { font-size: 13px; }
  .brand-copy small { font-size: 8px; }
  .back-button { gap: 6px; padding-left: 0; font-size: 10.5px; }
  .back-button span { width: 28px; height: 28px; }
  .page-intro h1 { font-size: 29px; letter-spacing: -1px; }
  .page-intro p:not(.eyebrow) { font-size: 12px; }
  .surface-card { padding: 18px; border-radius: 18px; }
  .section-heading { gap: 10px; margin-bottom: 20px; }
  .section-number { width: 38px; height: 38px; flex-basis: 38px; border-radius: 12px; }
  .section-heading-copy h2 { font-size: 15.5px; }
  .section-heading-copy p { font-size: 11px; }
  .required-note { display: none; }
  .fields-grid { grid-template-columns: minmax(0, 1fr); }
  .field-full { grid-column: auto; }
  .voucher-input-wrap { gap: 6px; }
  .ticket-icon { width: 34px; height: 36px; flex-basis: 34px; }
  .voucher-apply, .voucher-remove { padding: 0 12px; }
  .payment-option { padding: 13px; gap: 10px; }
  .payment-icon { width: 42px; height: 42px; flex-basis: 42px; border-radius: 13px; }
  .payment-copy strong { font-size: 12px; }
  .payment-copy small { font-size: 10.5px; }
  .qr-panel { padding: 14px; }
  .bank-layout { grid-template-columns: minmax(0, 1fr); }
  .qr-frame { max-width: 190px; margin: 0 auto; }
  .success-screen { padding: 24px 18px; border-radius: 22px; }
  .success-screen > h2 { font-size: 26px; }
  .success-order-number { flex-wrap: wrap; }
  .success-order-number > strong { min-width: 100px; }
  .success-details { grid-template-columns: minmax(0, 1fr); }
  .success-details .detail-wide { grid-column: auto; }
  .page-footer { align-items: flex-start; flex-direction: column; gap: 8px; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .checkout-page *,
  .checkout-page *::before,
  .checkout-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>