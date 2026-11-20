<template>
  <main class="search-page">
    <div class="ambient ambient-blue"></div>
    <div class="ambient ambient-red"></div>

    <section class="search-shell">
      <!-- RESULTS HEADER -->
      <header class="results-header">
        <div class="heading-copy">
          <span class="eyebrow">
            <span class="eyebrow-dot"></span>
            TECHSHOP / PRODUCT DISCOVERY
          </span>

          <h1>
            {{ keyword ? 'Kết quả tìm kiếm' : 'Khám phá sản phẩm' }}<span>.</span>
          </h1>

          <p>
            {{ keyword
              ? 'Các sản phẩm phù hợp với nhu cầu tìm kiếm của bạn.'
              : 'Khám phá thiết bị và linh kiện công nghệ tại TECHSHOP.'
            }}
          </p>

          <div v-if="keyword" class="active-query">
            <span class="query-label">Từ khóa</span>
            <strong>{{ keyword }}</strong>
            <button type="button" aria-label="Xóa từ khóa" @click="clearSearch">
              <i class="fa-solid fa-xmark"></i>
            </button>
          </div>
        </div>

        <div class="results-controls">
          <div class="result-count">
            <span class="count-icon"><i class="fa-solid fa-cube"></i></span>
            <span>
              <strong>{{ filteredProducts.length }}</strong>
              <small>sản phẩm</small>
            </span>
          </div>

          <label class="sort-control">
            <span>Sắp xếp</span>
            <select v-model="sortBy" aria-label="Sắp xếp sản phẩm">
              <option value="default">Mặc định</option>
              <option value="price-asc">Giá thấp đến cao</option>
              <option value="price-desc">Giá cao đến thấp</option>
              <option value="name">Tên A – Z</option>
            </select>
            <i class="fa-solid fa-chevron-down"></i>
          </label>
        </div>
      </header>

      <!-- LOADING SKELETON -->
      <section v-if="loading" class="product-grid" aria-label="Đang tải sản phẩm">
        <article v-for="n in 8" :key="n" class="product-card skeleton-card">
          <div class="skeleton-image"></div>
          <div class="skeleton-content">
            <span class="skeleton-line short"></span>
            <span class="skeleton-line"></span>
            <span class="skeleton-line medium"></span>
            <span class="skeleton-line price"></span>
            <span class="skeleton-button"></span>
          </div>
        </article>
      </section>

      <!-- API ERROR -->
      <section v-else-if="error" class="state-panel">
        <div class="state-icon error-icon">
          <i class="fa-solid fa-triangle-exclamation"></i>
        </div>
        <h2>Chưa thể tải sản phẩm</h2>
        <p>{{ error }}</p>
        <button class="state-action" type="button" @click="loadProducts">
          <i class="fa-solid fa-rotate-right"></i>
          Thử tải lại
        </button>
      </section>

      <!-- PRODUCTS -->
      <section v-else-if="filteredProducts.length" class="results-section">
        <div class="section-caption">
          <span>{{ keyword ? 'KẾT QUẢ PHÙ HỢP' : 'TẤT CẢ SẢN PHẨM' }}</span>
          <span class="caption-line"></span>
          <span>TECHSHOP COLLECTION</span>
        </div>

        <div class="product-grid">
          <article
            v-for="item in filteredProducts"
            :key="item.MaSP"
            class="product-card"
          >
            <!-- FIXED IMAGE STAGE: ảnh luôn nằm gọn trong khung -->
            <button
              type="button"
              class="product-visual"
              :aria-label="`Xem ${productName(item)}`"
              @click="openProduct(item)"
            >
              <div class="visual-frame">
                <img
                  v-if="productImage(item) && !brokenImages.includes(String(item.MaSP))"
                  :src="imageUrl(productImage(item))"
                  :alt="productName(item)"
                  loading="lazy"
                  @error="markImageBroken(item.MaSP)"
                />
                <div v-else class="image-placeholder">
                  <i class="fa-solid fa-box-open"></i>
                  <span>Chưa có hình ảnh</span>
                </div>
              </div>

              <span class="product-id">#{{ item.MaSP }}</span>
              <span class="visual-action"><i class="fa-solid fa-arrow-up-right-from-square"></i></span>
            </button>

            <!-- PRODUCT DETAILS -->
            <div class="product-content">
              <div class="product-overline">
                <span class="category-label">{{ productCategory(item) }}</span>
                <span class="selling-label">
                  <span></span> Đang kinh doanh
                </span>
              </div>

              <h2 class="product-name" :title="productName(item)">
                {{ productName(item) }}
              </h2>

              <p v-if="productDescription(item)" class="product-description">
                {{ productDescription(item) }}
              </p>
              <p v-else class="product-description description-placeholder">
                Thông tin chi tiết sản phẩm
              </p>

              <div class="product-divider"></div>

              <div class="product-bottom">
                <div class="price-block">
                  <span>GIÁ BÁN</span>
                  <strong>{{ formatMoney(item.GiaBan) }}</strong>
                </div>
                <span class="shop-mark">TECH<span>SHOP</span></span>
              </div>

              <button
                type="button"
                class="product-button"
                @click="openProduct(item)"
              >
                <span>Xem sản phẩm</span>
                <i class="fa-solid fa-arrow-right"></i>
              </button>
            </div>
          </article>
        </div>
      </section>

      <!-- NO RESULTS -->
      <section v-else class="state-panel empty-panel">
        <div class="empty-visual">
          <i class="fa-solid fa-magnifying-glass"></i>
          <span>?</span>
        </div>
        <span class="empty-eyebrow">NO MATCH FOUND</span>
        <h2>Chưa tìm thấy sản phẩm phù hợp</h2>
        <p>
          Không có sản phẩm phù hợp với
          <strong v-if="keyword">“{{ keyword }}”</strong>
          trong danh mục hiện tại. Bạn thử tên sản phẩm, mã sản phẩm hoặc tên linh kiện khác nhé.
        </p>
        <button class="state-action" type="button" @click="clearSearch">
          <i class="fa-solid fa-border-all"></i>
          Xem tất cả sản phẩm
        </button>
      </section>

      <footer class="search-footer">
        <span class="footer-brand">TECHSHOP<span>.</span></span>
        <span>Thiết bị phù hợp · Lựa chọn rõ ràng</span>
      </footer>
    </section>
  </main>
</template>

<script>
import axios from 'axios'

const API_ROOT = 'http://127.0.0.1:8000'
const API = `${API_ROOT}/api`

const SEARCH_ALIASES = [
  ['laptop', 'notebook', 'may tinh xach tay'],
  ['pc', 'desktop', 'may tinh de ban', 'may tinh ban', 'may bo'],
  ['gaming', 'choi game', 'game thu'],
  ['monitor', 'man hinh', 'display'],
  ['keyboard', 'ban phim'],
  ['mouse', 'chuot'],
  ['headset', 'headphone', 'tai nghe'],
  ['gpu', 'vga', 'card man hinh', 'card do hoa'],
  ['storage', 'ssd', 'hdd', 'o cung'],
  ['ram', 'bo nho'],
  ['psu', 'nguon may tinh'],
  ['mainboard', 'bo mach chu'],
  ['cpu', 'bo vi xu ly', 'vi xu ly'],
  ['case', 'vo case', 'vo may tinh'],
  ['cooler', 'tan nhiet', 'quat tan nhiet']
]

const PHRASE_REPLACEMENTS = [
  [/may tinh xach tay/g, 'laptop'],
  [/may tinh de ban/g, 'pc'],
  [/may tinh ban/g, 'pc'],
  [/may bo/g, 'pc'],
  [/choi game/g, 'gaming'],
  [/game thu/g, 'gaming'],
  [/card man hinh/g, 'gpu'],
  [/card do hoa/g, 'gpu'],
  [/bo vi xu ly/g, 'cpu'],
  [/vi xu ly/g, 'cpu'],
  [/man hinh/g, 'monitor'],
  [/ban phim/g, 'keyboard'],
  [/tai nghe/g, 'headset'],
  [/o cung/g, 'storage'],
  [/bo nho/g, 'ram'],
  [/vo may tinh/g, 'case'],
  [/tan nhiet/g, 'cooler']
]

export default {
  name: 'TimKiemSanPham',

  data() {
    return {
      keyword: '',
      products: [],
      loading: false,
      error: '',
      brokenImages: [],
      sortBy: 'default'
    }
  },

  computed: {
    filteredProducts() {
      let result = this.products.filter(item => item.TrangThaiSP !== 'NgungBan')

      if (this.keyword.trim()) {
        result = result.filter(item => this.matchesKeyword(item, this.keyword))
      }

      result = [...result]

      if (this.sortBy === 'price-asc') {
        result.sort((a, b) => Number(a.GiaBan || 0) - Number(b.GiaBan || 0))
      } else if (this.sortBy === 'price-desc') {
        result.sort((a, b) => Number(b.GiaBan || 0) - Number(a.GiaBan || 0))
      } else if (this.sortBy === 'name') {
        result.sort((a, b) => this.productName(a).localeCompare(this.productName(b), 'vi'))
      }

      return result
    }
  },

  watch: {
    '$route.query.q'(newValue) {
      this.keyword = String(newValue || '')
      this.sortBy = 'default'
    }
  },

  mounted() {
    // Đồng bộ đúng query q do thanh tìm kiếm trên TopClient truyền sang.
    this.keyword = String(this.$route.query.q || '')
    this.loadProducts()
  },

  methods: {
    normalizeText(value) {
      return String(value ?? '')
        .normalize('NFD')
        .replace(/[\u0300-\u036f]/g, '')
        .replace(/[đĐ]/g, 'd')
        .toLowerCase()
        .trim()
    },

    normalizeKeyword(value) {
      let query = this.normalizeText(value)

      for (const [pattern, replacement] of PHRASE_REPLACEMENTS) {
        query = query.replace(pattern, replacement)
      }

      return query.replace(/\s+/g, ' ').trim()
    },

    productSearchText(item) {
      const category = item?.danh_muc || item?.danhMuc || item?.loaiSanPham || {}
      const values = [
        item?.MaSP,
        item?.TenSP,
        item?.MoTa,
        item?.MoTaNgan,
        item?.MoTaChiTiet,
        item?.Model,
        item?.MaSKU,
        item?.TenDM,
        category?.TenDM,
        category?.TenDanhMuc,
        item?.ThongSo
      ]

      return this.normalizeText(values
        .map(value => {
          if (value && typeof value === 'object') {
            return JSON.stringify(value)
          }
          return String(value ?? '')
        })
        .join(' '))
    },

    matchesKeyword(product, rawKeyword) {
      const originalQuery = this.normalizeText(rawKeyword)

      if (!originalQuery) return true
      if (/^\d+$/.test(originalQuery)) {
        return String(product.MaSP) === originalQuery
      }

      const query = this.normalizeKeyword(originalQuery)
      const text = this.productSearchText(product)

      // "Máy tính" là từ khóa chung, bao gồm laptop và PC.
      if (query === 'may tinh' || query === 'computer') {
        return ['laptop', 'notebook', 'pc', 'desktop', 'computer']
          .some(term => text.includes(term))
      }

      const terms = query.split(' ').filter(Boolean)

      return terms.every(term => {
        const group = SEARCH_ALIASES.find(aliases =>
          aliases.some(alias => this.normalizeText(alias) === term)
        )

        if (group) {
          return group.some(alias => text.includes(this.normalizeText(alias)))
        }

        return text.includes(term)
      })
    },

    async loadProducts() {
      this.loading = true
      this.error = ''

      try {
        const response = await axios.get(`${API}/san-pham/get-data`, {
          timeout: 15000
        })

        if (response.data?.status === false) {
          throw new Error(response.data.message || 'Không thể tải danh sách sản phẩm.')
        }

        const result = response.data?.data
        let rows = []

        if (Array.isArray(result)) {
          rows = result
        } else if (Array.isArray(result?.data)) {
          rows = result.data
        } else {
          throw new Error('API không trả về danh sách sản phẩm hợp lệ.')
        }

        this.products = rows
        this.brokenImages = []
      } catch (err) {
        console.error('Lỗi tải sản phẩm tìm kiếm:', err)
        this.error = err?.response?.data?.message
          || err?.message
          || 'Không thể kết nối máy chủ sản phẩm.'
      } finally {
        this.loading = false
      }
    },

    productName(item) {
      return item?.TenSP || `Sản phẩm #${item?.MaSP ?? ''}`
    },

    productImage(item) {
      return item?.HinhAnh || item?.hinh || ''
    },

    productDescription(item) {
      return item?.MoTaNgan || item?.MoTa || item?.MoTaChiTiet || ''
    },

    productCategory(item) {
      const category = item?.danh_muc || item?.danhMuc || item?.loaiSanPham || {}
      return category?.TenDM || category?.TenDanhMuc || item?.TenDM || 'THIẾT BỊ CÔNG NGHỆ'
    },

    imageUrl(path) {
      if (!path) return ''

      const value = String(path).trim()
      if (/^https?:\/\//i.test(value)) return value

      if (
        value.startsWith('/images/') ||
        value.startsWith('/assets/') ||
        value.startsWith('/img_')
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

    markImageBroken(maSP) {
      const key = String(maSP)
      if (!this.brokenImages.includes(key)) {
        this.brokenImages = [...this.brokenImages, key]
      }
    },

    formatMoney(value) {
      return `${Number(value || 0).toLocaleString('vi-VN')} ₫`
    },

    clearSearch() {
      this.keyword = ''
      this.sortBy = 'default'

      this.$router.push({
        path: '/tim-kiem',
        query: { ...this.$route.query, q: undefined }
      })
    },

    openProduct(item) {
      if (!item?.MaSP) {
        window.alert('Không xác định được mã sản phẩm.')
        return
      }

      this.$router.push({
        path: '/chi-tiet-sp',
        query: { MaSP: String(item.MaSP) }
      })
    }
  }
}
</script>

<style scoped>
.search-page,
.search-page * {
  box-sizing: border-box;
}

.search-page {
  --ink: #17233d;
  --muted: #7c899f;
  --blue: #315ee8;
  --line: #e6ebf3;
  position: relative;
  isolation: isolate;
  min-height: calc(100vh - 88px);
  overflow: hidden;
  padding: 42px 28px 34px;
  background: #f5f7fb;
  color: var(--ink);
  font-family: 'Be Vietnam Pro', 'Inter', system-ui, sans-serif;
}

.ambient {
  position: absolute;
  z-index: -1;
  width: 430px;
  height: 430px;
  border-radius: 50%;
  filter: blur(95px);
  pointer-events: none;
  opacity: 0.38;
}

.ambient-blue {
  top: -230px;
  left: -140px;
  background: #d6e2ff;
}

.ambient-red {
  top: 260px;
  right: -300px;
  background: #ffe1e6;
  opacity: 0.24;
}

.search-shell {
  width: min(1510px, 100%);
  margin: 0 auto;
}

.results-header {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 28px;
  margin-bottom: 30px;
}

.heading-copy {
  min-width: 0;
}

.eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  color: #4969cb;
  font-size: 10px;
  font-weight: 850;
  letter-spacing: 1.8px;
}

.eyebrow-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #4169ee;
  box-shadow: 0 0 0 4px #e5ebff;
}

.results-header h1 {
  margin: 13px 0 8px;
  color: #17233d;
  font-size: clamp(28px, 3.2vw, 39px);
  font-weight: 850;
  letter-spacing: -1.3px;
  line-height: 1.18;
}

.results-header h1 span {
  color: #4168ed;
}

.heading-copy > p {
  margin: 0;
  color: #7d899e;
  font-size: 13px;
  line-height: 1.7;
}

.active-query {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  margin-top: 15px;
  padding: 7px 9px 7px 12px;
  border: 1px solid #dce5ff;
  border-radius: 999px;
  background: #fff;
  color: #3453aa;
  font-size: 11px;
}

.query-label {
  color: #8995aa;
}

.active-query strong {
  font-size: 11px;
}

.active-query button {
  display: grid;
  width: 22px;
  height: 22px;
  padding: 0;
  place-items: center;
  border: 0;
  border-radius: 50%;
  background: #edf2ff;
  color: #4563bc;
  cursor: pointer;
}

.results-controls {
  display: flex;
  flex-shrink: 0;
  align-items: center;
  gap: 12px;
}

.result-count {
  display: flex;
  align-items: center;
  gap: 10px;
  min-width: 118px;
  padding: 10px 13px;
  border: 1px solid #e4eaf4;
  border-radius: 13px;
  background: rgba(255, 255, 255, 0.82);
}

.count-icon {
  display: grid;
  width: 34px;
  height: 34px;
  place-items: center;
  border-radius: 10px;
  background: #edf2ff;
  color: #4165df;
}

.result-count strong,
.result-count small {
  display: block;
}

.result-count strong {
  color: #1c2a44;
  font-size: 15px;
  line-height: 1.1;
}

.result-count small {
  margin-top: 3px;
  color: #8894a8;
  font-size: 10px;
}

.sort-control {
  position: relative;
  display: flex;
  flex-direction: column;
  gap: 4px;
  min-width: 170px;
  padding: 8px 34px 8px 12px;
  border: 1px solid #e4eaf4;
  border-radius: 13px;
  background: rgba(255, 255, 255, 0.9);
}

.sort-control > span {
  color: #8a95a8;
  font-size: 9px;
  font-weight: 700;
}

.sort-control select {
  width: 100%;
  appearance: none;
  border: 0;
  outline: 0;
  background: transparent;
  color: #293853;
  font: inherit;
  font-size: 11px;
  font-weight: 750;
  cursor: pointer;
}

.sort-control > i {
  position: absolute;
  top: 50%;
  right: 13px;
  color: #7f8ca2;
  font-size: 10px;
  pointer-events: none;
  transform: translateY(-10%);
}

.section-caption {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 15px;
  color: #9aa5b8;
  font-size: 9px;
  font-weight: 800;
  letter-spacing: 1.2px;
}

.caption-line {
  width: 34px;
  height: 1px;
  background: #cfd8e9;
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 22px;
  align-items: stretch;
}

.product-card {
  display: flex;
  min-width: 0;
  flex-direction: column;
  overflow: hidden;
  border: 1px solid #e5eaf3;
  border-radius: 19px;
  background: #fff;
  box-shadow: 0 8px 28px rgba(30, 48, 82, 0.045);
  transition: transform 0.22s ease, box-shadow 0.22s ease, border-color 0.22s ease;
}

.product-card:hover {
  transform: translateY(-5px);
  border-color: #d8e1f6;
  box-shadow: 0 18px 38px rgba(30, 48, 82, 0.095);
}

.product-visual {
  position: relative;
  display: block;
  width: 100%;
  height: 226px;
  min-height: 226px;
  margin: 0;
  padding: 0;
  overflow: hidden;
  border: 0;
  border-bottom: 1px solid #edf0f6;
  border-radius: 0;
  background: linear-gradient(145deg, #f8faff 0%, #eef2f9 100%);
  cursor: pointer;
}

.visual-frame {
  display: flex;
  width: 100%;
  height: 100%;
  min-width: 0;
  min-height: 0;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: 15px;
}

.visual-frame img {
  display: block;
  width: 100%;
  height: 100%;
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  object-position: center;
  transition: transform 0.28s ease;
}

.product-card:hover .visual-frame img {
  transform: scale(1.035);
}

.product-id {
  position: absolute;
  top: 13px;
  left: 13px;
  padding: 6px 9px;
  border: 1px solid rgba(224, 231, 243, 0.95);
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.94);
  color: #64738f;
  font-size: 10px;
  font-weight: 850;
}

.visual-action {
  position: absolute;
  right: 13px;
  bottom: 13px;
  display: grid;
  width: 31px;
  height: 31px;
  place-items: center;
  border: 1px solid rgba(225, 232, 244, 0.9);
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.92);
  color: #4c69cf;
  font-size: 11px;
  opacity: 0;
  transform: translateY(4px);
  transition: 0.2s ease;
}

.product-card:hover .visual-action {
  opacity: 1;
  transform: translateY(0);
}

.image-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 9px;
  color: #9ba8bc;
  font-size: 11px;
}

.image-placeholder i {
  font-size: 32px;
}

.product-content {
  display: flex;
  flex: 1;
  min-width: 0;
  flex-direction: column;
  padding: 17px 17px 16px;
}

.product-overline {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  min-height: 17px;
}

.category-label {
  overflow: hidden;
  color: #6379b5;
  font-size: 9px;
  font-weight: 850;
  letter-spacing: 0.9px;
  text-overflow: ellipsis;
  text-transform: uppercase;
  white-space: nowrap;
}

.selling-label {
  display: inline-flex;
  flex-shrink: 0;
  align-items: center;
  gap: 5px;
  color: #26916e;
  font-size: 9px;
  font-weight: 750;
}

.selling-label span {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #31b486;
  box-shadow: 0 0 0 3px #e5f7f0;
}

.product-name {
  display: -webkit-box;
  min-height: 48px;
  margin: 11px 0 6px;
  overflow: hidden;
  color: #1b2943;
  font-size: 15px;
  font-weight: 820;
  line-height: 1.55;
  overflow-wrap: anywhere;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}

.product-description {
  display: -webkit-box;
  min-height: 35px;
  margin: 0;
  overflow: hidden;
  color: #8792a5;
  font-size: 10.5px;
  line-height: 1.65;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}

.description-placeholder {
  color: #b0b8c5;
}

.product-divider {
  height: 1px;
  margin: 14px 0 12px;
  background: #edf0f5;
}

.product-bottom {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 8px;
  margin-top: auto;
}

.price-block > span,
.price-block > strong {
  display: block;
}

.price-block > span {
  margin-bottom: 5px;
  color: #9aa4b5;
  font-size: 8px;
  font-weight: 800;
  letter-spacing: 0.9px;
}

.price-block > strong {
  color: #e4495b;
  font-size: 19px;
  font-weight: 900;
  letter-spacing: -0.4px;
  white-space: nowrap;
}

.shop-mark {
  padding-bottom: 3px;
  color: #1c936f;
  font-size: 9px;
  font-weight: 850;
  letter-spacing: 0.6px;
  white-space: nowrap;
}

.shop-mark span {
  color: #2d405e;
}

.product-button {
  display: flex;
  width: 100%;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  margin-top: 15px;
  padding: 12px 14px;
  border: 1px solid #dce5ff;
  border-radius: 11px;
  background: #f4f6ff;
  color: #315bd9;
  font: inherit;
  font-size: 11px;
  font-weight: 850;
  cursor: pointer;
  transition: background 0.2s ease, color 0.2s ease, border-color 0.2s ease;
}

.product-button span {
  flex: 1;
  text-align: center;
}

.product-button i {
  font-size: 11px;
  transition: transform 0.2s ease;
}

.product-button:hover {
  border-color: #315bd9;
  background: #315bd9;
  color: #fff;
}

.product-button:hover i {
  transform: translateX(3px);
}

/* Loading skeleton */
.skeleton-card {
  pointer-events: none;
}

.skeleton-image,
.skeleton-line,
.skeleton-button {
  background: linear-gradient(90deg, #edf1f7 25%, #f7f9fc 40%, #edf1f7 65%);
  background-size: 300% 100%;
  animation: shimmer 1.35s ease infinite;
}

.skeleton-image {
  height: 226px;
}

.skeleton-content {
  display: flex;
  flex: 1;
  flex-direction: column;
  gap: 12px;
  padding: 18px;
}

.skeleton-line {
  width: 100%;
  height: 12px;
  border-radius: 7px;
}

.skeleton-line.short { width: 42%; height: 9px; }
.skeleton-line.medium { width: 72%; }
.skeleton-line.price { width: 55%; height: 19px; margin-top: auto; }
.skeleton-button { height: 42px; margin-top: 4px; border-radius: 10px; }

@keyframes shimmer {
  0% { background-position: 100% 0; }
  100% { background-position: -100% 0; }
}

/* Empty and error states */
.state-panel {
  display: flex;
  min-height: 390px;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 44px 24px;
  border: 1px solid #e5eaf3;
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.92);
  box-shadow: 0 12px 35px rgba(30, 48, 82, 0.045);
  text-align: center;
}

.state-icon,
.empty-visual {
  display: grid;
  width: 76px;
  height: 76px;
  margin-bottom: 19px;
  place-items: center;
  border-radius: 23px;
  background: #eef2ff;
  color: #6680d7;
  font-size: 28px;
}

.error-icon {
  background: #fff0f0;
  color: #dc646c;
}

.state-panel h2 {
  margin: 0 0 9px;
  color: #1d2b45;
  font-size: 22px;
  font-weight: 850;
}

.state-panel p {
  max-width: 520px;
  margin: 0 0 22px;
  color: #7d899e;
  font-size: 12px;
  line-height: 1.8;
}

.state-panel p strong {
  color: #3454af;
}

.empty-visual {
  position: relative;
  font-size: 29px;
}

.empty-visual span {
  position: absolute;
  top: -5px;
  right: -5px;
  display: grid;
  width: 25px;
  height: 25px;
  place-items: center;
  border: 3px solid white;
  border-radius: 50%;
  background: #f06e7b;
  color: white;
  font-size: 12px;
  font-weight: 900;
}

.empty-eyebrow {
  margin-bottom: 10px;
  color: #91a0ba;
  font-size: 9px;
  font-weight: 850;
  letter-spacing: 1.5px;
}

.state-action {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  min-height: 42px;
  padding: 0 18px;
  border: 0;
  border-radius: 11px;
  background: #315ee8;
  color: #fff;
  font: inherit;
  font-size: 11px;
  font-weight: 850;
  cursor: pointer;
  box-shadow: 0 8px 18px rgba(49, 94, 232, 0.18);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.state-action:hover {
  transform: translateY(-2px);
  box-shadow: 0 11px 22px rgba(49, 94, 232, 0.24);
}

.search-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 14px;
  margin-top: 34px;
  padding-top: 18px;
  border-top: 1px solid #e4e9f1;
  color: #929db0;
  font-size: 10px;
}

.footer-brand {
  color: #344866;
  font-size: 12px;
  font-weight: 900;
  letter-spacing: 1.4px;
}

.footer-brand span {
  color: #4168ed;
}

@media (max-width: 1180px) {
  .product-grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}

@media (max-width: 850px) {
  .search-page {
    padding: 30px 18px;
  }

  .results-header {
    align-items: flex-start;
    flex-direction: column;
  }

  .results-controls {
    width: 100%;
    justify-content: space-between;
  }

  .product-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 15px;
  }
}

@media (max-width: 520px) {
  .search-page {
    padding: 23px 12px 28px;
  }

  .results-header h1 {
    font-size: 28px;
  }

  .results-controls {
    align-items: stretch;
  }

  .result-count {
    min-width: 100px;
    padding: 8px;
  }

  .sort-control {
    min-width: 0;
    flex: 1;
  }

  .product-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 11px;
  }

  .product-visual,
  .skeleton-image {
    height: 160px;
    min-height: 160px;
  }

  .visual-frame {
    padding: 9px;
  }

  .product-content {
    padding: 12px;
  }

  .product-overline {
    align-items: flex-start;
    flex-direction: column;
    gap: 5px;
  }

  .product-name {
    min-height: 42px;
    font-size: 12px;
  }

  .product-description {
    min-height: 31px;
    font-size: 9px;
  }

  .price-block > strong {
    font-size: 14px;
  }

  .shop-mark {
    font-size: 8px;
  }

  .product-button {
    padding: 10px 8px;
    font-size: 10px;
  }

  .search-footer {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
