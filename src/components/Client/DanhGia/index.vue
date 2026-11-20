
<template>
    <main class="review-page">
      <div class="review-orb orb-one"></div>
      <div class="review-orb orb-two"></div>
  
      <div class="review-shell">
        <header class="review-topbar">
          <button class="back-button" type="button" @click="goBack">
            <i class="fa-solid fa-arrow-left"></i>
            Quay lại
          </button>
  
          <div class="review-breadcrumb">
            TÀI KHOẢN <span>/</span> ĐÁNH GIÁ SẢN PHẨM
          </div>
        </header>
  
        <div class="review-layout">
          <!-- THÔNG TIN SẢN PHẨM -->
          <aside class="product-panel">
            <span class="eyebrow">
              <i class="fa-solid fa-shield-heart"></i>
              TECHSHOP EXPERIENCE
            </span>
  
            <h1>Chia sẻ trải nghiệm mua sắm của bạn.</h1>
  
            <p class="panel-description">
              Đánh giá chân thực của bạn giúp những khách hàng khác
              lựa chọn sản phẩm phù hợp hơn.
            </p>
  
            <div v-if="loadingProduct" class="product-loading">
              <span class="spinner"></span>
              Đang tải sản phẩm...
            </div>
  
            <div v-else-if="product" class="product-preview">
              <div class="product-image-wrap">
                <img
                  v-if="product.HinhAnh && !imageError"
                  :src="imageUrl(product.HinhAnh)"
                  :alt="product.TenSP"
                  @error="imageError = true"
                />
  
                <div v-else class="image-fallback">
                  <i class="fa-solid fa-box-open"></i>
                </div>
  
                <span class="product-tag">SẢN PHẨM ĐÃ MUA</span>
              </div>
  
              <div class="product-copy">
                <span class="product-code">
                  MÃ SẢN PHẨM #{{ product.MaSP }}
                </span>
  
                <h2>{{ product.TenSP }}</h2>
  
                <p v-if="product.MoTa || product.MoTaChiTiet">
                  {{ product.MoTa || product.MoTaChiTiet }}
                </p>
  
                <strong class="product-price">
                  {{ formatMoney(product.GiaBan) }}
                </strong>
              </div>
            </div>
  
            <div class="privacy-note">
              <i class="fa-solid fa-circle-check"></i>
              <span>
                Đánh giá được gửi bằng tài khoản đang đăng nhập.
              </span>
            </div>
          </aside>
  
          <!-- FORM ĐÁNH GIÁ -->
          <section class="review-form-panel">
            <div class="form-heading">
              <span class="form-number">01 — YOUR REVIEW</span>
              <h2>Đánh giá sản phẩm</h2>
              <p>Hãy cho chúng tôi biết trải nghiệm thực tế của bạn.</p>
            </div>
  
            <div v-if="error" class="form-alert alert-error">
              <i class="fa-solid fa-circle-exclamation"></i>
              <span>{{ error }}</span>
            </div>
  
            <div
              v-if="success"
              class="success-panel"
              role="status"
            >
              <div class="success-icon">
                <i class="fa-solid fa-check"></i>
              </div>
  
              <h3>Đã gửi đánh giá</h3>
              <p>{{ success }}</p>
  
              <button
                type="button"
                class="submit-button"
                @click="goHistory"
              >
                Về lịch sử mua hàng
                <i class="fa-solid fa-arrow-right"></i>
              </button>
            </div>
  
            <div v-else-if="!loadingProduct && product">
              <!-- CHỌN SỐ SAO -->
              <div class="rating-section">
                <label class="field-label">
                  Bạn đánh giá chất lượng sản phẩm thế nào?
                  <span>*</span>
                </label>
  
                <div class="rating-box">
                  <div class="rating-stars">
                    <button
                      v-for="star in 5"
                      :key="star"
                      type="button"
                      class="star-button"
                      :class="{ selected: star <= rating }"
                      :aria-label="`Đánh giá ${star} sao`"
                      :aria-pressed="rating === star"
                      @click="rating = star"
                    >
                      <i class="fa-solid fa-star"></i>
                    </button>
                  </div>
  
                  <div class="rating-caption">
                    <strong>
                      {{
                        [
                          'Chưa đánh giá',
                          'Rất không hài lòng',
                          'Chưa hài lòng',
                          'Bình thường',
                          'Hài lòng',
                          'Rất hài lòng'
                        ][rating]
                      }}
                    </strong>
  
                    <span>{{ rating }}/5 sao</span>
                  </div>
                </div>
              </div>
  
              <!-- NHẬN XÉT -->
              <div class="comment-section">
                <label class="field-label" for="review-comment">
                  Nhận xét của bạn <span>*</span>
                </label>
  
                <textarea
                  id="review-comment"
                  v-model="comment"
                  rows="6"
                  maxlength="1000"
                  placeholder="Chia sẻ về chất lượng, hiệu năng, đóng gói hoặc trải nghiệm sử dụng..."
                  :disabled="submitting"
                ></textarea>
  
                <div class="comment-footer">
                  <span>Hãy viết nhận xét lịch sự và chân thực.</span>
                  <span>{{ comment.length }}/1000</span>
                </div>
              </div>
  
              <div class="form-actions">
                <button
                  type="button"
                  class="secondary-button"
                  :disabled="submitting"
                  @click="goBack"
                >
                  Để sau
                </button>
  
                <button
                  type="button"
                  class="submit-button"
                  :disabled="submitting"
                  @click="submitReview"
                >
                  <span v-if="submitting" class="button-spinner"></span>
                  <i v-else class="fa-solid fa-paper-plane"></i>
  
                  {{ submitting ? 'Đang gửi...' : 'Gửi đánh giá' }}
                </button>
              </div>
  
              <p class="form-footnote">
                <i class="fa-solid fa-lock"></i>
                Vui lòng chỉ đánh giá sản phẩm bạn đã mua.
              </p>
            </div>
  
            <div
              v-else-if="!loadingProduct && !product"
              class="unavailable-panel"
            >
              <i class="fa-solid fa-box-open"></i>
              <h3>Chưa thể mở sản phẩm</h3>
              <p>Kiểm tra lại đơn hàng hoặc quay về lịch sử mua hàng.</p>
  
              <button
                type="button"
                class="submit-button"
                @click="goHistory"
              >
                Về lịch sử mua hàng
              </button>
            </div>
          </section>
        </div>
  
        <footer class="review-footer">
          <strong>TECHSHOP</strong>
          <span>Trải nghiệm của bạn tạo nên sự khác biệt.</span>
        </footer>
      </div>
    </main>
  </template>
  
  <script>
  import axios from 'axios'
  
  const API_ROOT = 'http://127.0.0.1:8000'
  const API = `${API_ROOT}/api`
  
  export default {
    name: 'DanhGiaSanPham',
  
    data() {
      return {
        product: null,
        rating: 0,
        comment: '',
  
        loadingProduct: false,
        submitting: false,
        imageError: false,
  
        error: '',
        success: ''
      }
    },
  
    watch: {
      '$route.query.MaSP'() {
        this.rating = 0
        this.comment = ''
        this.success = ''
        this.loadProduct()
      }
    },
  
    mounted() {
      this.loadProduct()
    },
  
    methods: {
      getToken() {
        return (
          localStorage.getItem('token') ||
          localStorage.getItem('client_token') ||
          localStorage.getItem('user_token')
        )
      },
  
      requestConfig() {
        const token = this.getToken()
  
        return {
          headers: token
            ? { Authorization: `Bearer ${token}` }
            : {}
        }
      },
  
      async loadProduct() {
        const maSP = Number(this.$route.query.MaSP || 0)
  
        this.product = null
        this.imageError = false
        this.error = ''
  
        if (!Number.isInteger(maSP) || maSP <= 0) {
          this.error = 'Không xác định được mã sản phẩm từ đơn hàng.'
          return
        }
  
        this.loadingProduct = true
  
        try {
          const response = await axios.get(
            `${API}/san-pham/get-data`
          )
  
          if (response.data?.status === false) {
            throw new Error(
              response.data.message || 'Không tải được sản phẩm.'
            )
          }
  
          const products = Array.isArray(response.data?.data)
            ? response.data.data
            : []
  
          const found = products.find(
            item => Number(item.MaSP) === maSP
          )
  
          if (!found) {
            throw new Error('Không tìm thấy sản phẩm cần đánh giá.')
          }
  
          this.product = found
        } catch (err) {
          this.error =
            err?.response?.data?.message ||
            err.message ||
            'Không thể tải thông tin sản phẩm.'
        } finally {
          this.loadingProduct = false
        }
      },
  
      async submitReview() {
        if (this.submitting) return
  
        if (!this.getToken()) {
          this.error = 'Phiên đăng nhập không hợp lệ. Vui lòng đăng nhập lại.'
          return
        }
  
        if (!this.product?.MaSP) {
          this.error = 'Chưa xác định được sản phẩm cần đánh giá.'
          return
        }
  
        if (this.rating < 1 || this.rating > 5) {
          this.error = 'Vui lòng chọn số sao từ 1 đến 5.'
          return
        }
  
        const noiDung = this.comment.trim()
  
        if (!noiDung) {
          this.error = 'Vui lòng nhập nhận xét trước khi gửi.'
          return
        }
  
        this.error = ''
        this.submitting = true
  
        try {
          const response = await axios.post(
            `${API}/danh-gia/add-data`,
            {
              MaSP: Number(this.product.MaSP),
              SoSao: this.rating,
              NoiDung: noiDung
            },
            this.requestConfig()
          )
  
          if (response.data?.status === false) {
            throw new Error(
              response.data.message || 'Gửi đánh giá thất bại.'
            )
          }
  
          this.success =
            response.data?.message ||
            'Cảm ơn bạn đã chia sẻ trải nghiệm sản phẩm.'
        } catch (err) {
          this.error =
            err?.response?.data?.message ||
            (
              err?.response?.data?.errors &&
              Object.values(err.response.data.errors).flat().join(' ')
            ) ||
            err.message ||
            'Không thể gửi đánh giá. Vui lòng thử lại.'
        } finally {
          this.submitting = false
        }
      },
  
      imageUrl(path) {
        if (!path) return ''
  
        const value = String(path)
  
        if (/^https?:\/\//i.test(value)) return value
  
        if (
          value.startsWith('/images/') ||
          value.startsWith('/assets/')
        ) {
          return value
        }
  
        if (value.startsWith('/storage/')) {
          return `${API_ROOT}${value}`
        }
  
        return `${API_ROOT}/storage/${value
          .replace(/^\/?storage\//, '')
          .replace(/^\/+/, '')}`
      },
  
      formatMoney(value) {
        return `${Number(value || 0).toLocaleString('vi-VN')} ₫`
      },
  
      goBack() {
        if (window.history.length > 1) {
          this.$router.back()
        } else {
          this.goHistory()
        }
      },
  
      goHistory() {
        const candidates = [
          '/lich-su-mua-hang',
          '/client/lich-su-mua-hang',
          '/don-hang'
        ]
  
        const target = candidates.find(
          path => this.$router.resolve(path).matched.length > 0
        )
  
        this.$router.push(target || '/')
      }
    }
  }
  </script>
  
  <style scoped>
  .review-page {
    --ink: #17233d;
    --muted: #7b879d;
    --line: #e5eaf3;
    --blue: #315fe8;
    position: relative;
    isolation: isolate;
    min-height: 100vh;
    overflow: hidden;
    padding: 26px 22px 30px;
    background: linear-gradient(135deg, #f7f9fe, #edf2fb);
    color: var(--ink);
    font-family: 'Be Vietnam Pro', sans-serif;
  }
  
  .review-orb {
    position: absolute;
    z-index: -1;
    width: 360px;
    height: 360px;
    border-radius: 50%;
    filter: blur(70px);
    opacity: 0.55;
    pointer-events: none;
  }
  
  .orb-one {
    top: -170px;
    right: -100px;
    background: #d8e3ff;
  }
  
  .orb-two {
    bottom: -190px;
    left: -120px;
    background: #fff0d5;
  }
  
  .review-shell {
    width: min(1120px, 100%);
    margin: 0 auto;
  }
  
  .review-topbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    margin-bottom: 24px;
  }
  
  .back-button {
    display: inline-flex;
    align-items: center;
    gap: 9px;
    padding: 10px 14px;
    border: 1px solid #dfe5f0;
    border-radius: 11px;
    background: rgba(255, 255, 255, 0.8);
    color: var(--ink);
    font-weight: 700;
    cursor: pointer;
  }
  
  .review-breadcrumb {
    color: #8490a6;
    font-size: 11px;
    font-weight: 800;
    letter-spacing: 1.2px;
  }
  
  .review-breadcrumb span {
    padding: 0 6px;
    color: var(--blue);
  }
  
  .review-layout {
    display: grid;
    grid-template-columns: minmax(0, 0.9fr) minmax(0, 1.1fr);
    gap: 22px;
    align-items: stretch;
  }
  
  .product-panel,
  .review-form-panel {
    min-width: 0;
    border: 1px solid rgba(224, 231, 243, 0.95);
    border-radius: 22px;
    background: rgba(255, 255, 255, 0.94);
    box-shadow: 0 18px 55px rgba(40, 58, 99, 0.07);
  }
  
  .product-panel {
    display: flex;
    flex-direction: column;
    padding: 30px;
    background:
      radial-gradient(circle at 90% 5%, rgba(205, 220, 255, 0.7), transparent 28%),
      rgba(255, 255, 255, 0.95);
  }
  
  .eyebrow,
  .form-number {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    color: var(--blue);
    font-size: 11px;
    font-weight: 800;
    letter-spacing: 1.2px;
  }
  
  .product-panel h1 {
    margin: 22px 0 13px;
    font-size: clamp(27px, 3vw, 37px);
    font-weight: 800;
    line-height: 1.25;
    letter-spacing: -1.2px;
  }
  
  .panel-description {
    margin: 0;
    color: var(--muted);
    font-size: 14px;
    line-height: 1.8;
  }
  
  .product-preview {
    margin-top: 26px;
    overflow: hidden;
    border: 1px solid var(--line);
    border-radius: 17px;
    background: #fff;
  }
  
  .product-image-wrap {
    position: relative;
    display: grid;
    min-height: 235px;
    padding: 22px;
    place-items: center;
    background: linear-gradient(135deg, #f4f7ff, #eef2fa);
  }
  
  .product-image-wrap img {
    width: 100%;
    height: 205px;
    object-fit: contain;
  }
  
  .image-fallback {
    display: grid;
    width: 100%;
    min-height: 190px;
    place-items: center;
    color: #9caac2;
    font-size: 42px;
  }
  
  .product-tag {
    position: absolute;
    top: 15px;
    left: 15px;
    padding: 7px 9px;
    border: 1px solid #dce5fb;
    border-radius: 7px;
    background: #fff;
    color: #4560aa;
    font-size: 9px;
    font-weight: 800;
    letter-spacing: 0.6px;
  }
  
  .product-copy {
    padding: 20px;
  }
  
  .product-code {
    color: #8793a8;
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 0.8px;
  }
  
  .product-copy h2 {
    margin: 9px 0;
    font-size: 19px;
    line-height: 1.45;
    font-weight: 800;
  }
  
  .product-copy p {
    margin: 0 0 12px;
    color: var(--muted);
    font-size: 12px;
    line-height: 1.7;
  }
  
  .product-price {
    color: var(--blue);
    font-size: 19px;
    font-weight: 800;
  }
  
  .privacy-note {
    display: flex;
    align-items: flex-start;
    gap: 10px;
    margin-top: auto;
    padding-top: 24px;
    color: #64738c;
    font-size: 12px;
    line-height: 1.7;
  }
  
  .privacy-note i {
    margin-top: 3px;
    color: #20a579;
  }
  
  .review-form-panel {
    padding: 34px;
  }
  
  .form-heading {
    margin-bottom: 26px;
  }
  
  .form-heading h2 {
    margin: 12px 0 7px;
    font-size: 27px;
    font-weight: 800;
    letter-spacing: -0.8px;
  }
  
  .form-heading p {
    margin: 0;
    color: var(--muted);
    font-size: 13px;
    line-height: 1.7;
  }
  
  .field-label {
    display: block;
    margin-bottom: 12px;
    font-size: 14px;
    font-weight: 800;
  }
  
  .field-label span {
    color: #e05252;
  }
  
  .rating-section {
    margin-bottom: 27px;
  }
  
  .rating-box {
    padding: 20px;
    border: 1px solid #e9edf5;
    border-radius: 15px;
    background: #fafbfe;
  }
  
  .rating-stars {
    display: flex;
    gap: 10px;
  }
  
  .star-button {
    display: grid;
    width: 43px;
    height: 43px;
    padding: 0;
    border: 1px solid #e5e9f2;
    border-radius: 12px;
    background: #fff;
    color: #c5ccda;
    font-size: 19px;
    cursor: pointer;
    transition: transform 0.18s, color 0.18s, border-color 0.18s;
  }
  
  .star-button:hover {
    transform: translateY(-2px);
  }
  
  .star-button.selected {
    border-color: #f4cb62;
    background: #fff7dc;
    color: #f0b82e;
  }
  
  .rating-caption {
    display: flex;
    justify-content: space-between;
    gap: 12px;
    margin-top: 14px;
    color: var(--muted);
    font-size: 12px;
  }
  
  .rating-caption strong {
    color: #293855;
  }
  
  .comment-section textarea {
    display: block;
    width: 100%;
    min-height: 145px;
    padding: 15px;
    border: 1px solid #dfe5ef;
    border-radius: 13px;
    background: #fff;
    color: var(--ink);
    font: inherit;
    font-size: 13px;
    line-height: 1.7;
    resize: vertical;
    outline: none;
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  
  .comment-section textarea:focus {
    border-color: #7291f4;
    box-shadow: 0 0 0 4px rgba(49, 95, 232, 0.09);
  }
  
  .comment-section textarea::placeholder {
    color: #a3adbd;
  }
  
  .comment-footer {
    display: flex;
    justify-content: space-between;
    gap: 12px;
    margin-top: 8px;
    color: #8994a7;
    font-size: 10px;
    line-height: 1.6;
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 11px;
    margin-top: 25px;
  }
  
  .secondary-button,
  .submit-button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 9px;
    min-height: 45px;
    padding: 0 17px;
    border-radius: 11px;
    font-size: 13px;
    font-weight: 800;
    cursor: pointer;
    transition: transform 0.2s, opacity 0.2s;
  }
  
  .secondary-button {
    border: 1px solid #dce3ee;
    background: #fff;
    color: #596780;
  }
  
  .submit-button {
    border: 0;
    background: linear-gradient(100deg, #416af0, #2854d9);
    color: #fff;
    box-shadow: 0 7px 18px rgba(49, 95, 232, 0.2);
  }
  
  .submit-button:hover,
  .secondary-button:hover {
    transform: translateY(-1px);
  }
  
  .submit-button:disabled,
  .secondary-button:disabled {
    cursor: not-allowed;
    opacity: 0.65;
    transform: none;
  }
  
  .form-footnote {
    display: flex;
    align-items: center;
    gap: 7px;
    margin: 20px 0 0;
    color: #8490a3;
    font-size: 11px;
  }
  
  .form-footnote i {
    color: #20a579;
  }
  
  .form-alert {
    display: flex;
    align-items: flex-start;
    gap: 10px;
    margin: 0 0 20px;
    padding: 13px 15px;
    border-radius: 11px;
    font-size: 12px;
    line-height: 1.7;
  }
  
  .alert-error {
    border: 1px solid #fecaca;
    background: #fff4f4;
    color: #b42318;
  }
  
  .success-panel,
  .unavailable-panel {
    padding: 35px 10px;
    text-align: center;
  }
  
  .success-icon {
    display: grid;
    width: 58px;
    height: 58px;
    margin: 0 auto 16px;
    place-items: center;
    border-radius: 50%;
    background: #e3f8ef;
    color: #12966b;
    font-size: 24px;
  }
  
  .success-panel h3,
  .unavailable-panel h3 {
    margin: 0 0 8px;
    font-size: 21px;
    font-weight: 800;
  }
  
  .success-panel p,
  .unavailable-panel p {
    margin: 0 auto 20px;
    max-width: 350px;
    color: var(--muted);
    font-size: 13px;
    line-height: 1.8;
  }
  
  .unavailable-panel > i {
    display: block;
    margin-bottom: 16px;
    color: #8e9ab0;
    font-size: 32px;
  }
  
  .product-loading {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-top: 28px;
    color: var(--muted);
    font-size: 13px;
  }
  
  .spinner,
  .button-spinner {
    width: 17px;
    height: 17px;
    border: 2px solid #dce4f7;
    border-top-color: var(--blue);
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }
  
  .button-spinner {
    width: 14px;
    height: 14px;
    border-color: rgba(255, 255, 255, 0.4);
    border-top-color: #fff;
  }
  
  .review-footer {
    display: flex;
    justify-content: space-between;
    gap: 12px;
    margin-top: 26px;
    color: #8b96a9;
    font-size: 11px;
  }
  
  .review-footer strong {
    color: #354766;
    letter-spacing: 1.5px;
  }
  
  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }
  
  @media (max-width: 800px) {
    .review-page {
      padding: 16px 12px 22px;
    }
  
    .review-layout {
      grid-template-columns: 1fr;
    }
  
    .product-panel,
    .review-form-panel {
      padding: 22px;
      border-radius: 18px;
    }
  
    .product-panel h1 {
      margin-top: 15px;
      font-size: 28px;
    }
  
    .product-image-wrap {
      min-height: 190px;
    }
  
    .product-image-wrap img {
      height: 170px;
    }
  
    .review-topbar {
      align-items: flex-start;
    }
  
    .review-breadcrumb {
      padding-top: 11px;
      text-align: right;
      font-size: 9px;
    }
  
    .form-heading h2 {
      font-size: 24px;
    }
  
    .rating-stars {
      gap: 7px;
    }
  
    .star-button {
      width: 39px;
      height: 39px;
    }
  
    .review-footer {
      flex-direction: column;
    }
  }
  
  @media (max-width: 420px) {
    .form-actions {
      flex-direction: column-reverse;
    }
  
    .form-actions button {
      width: 100%;
    }
  
    .rating-caption {
      flex-direction: column;
      gap: 5px;
    }
  }
  </style>
  