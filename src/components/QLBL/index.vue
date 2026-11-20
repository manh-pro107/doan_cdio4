
<template>
  <div class="comment-page">
    <!-- Tiêu đề -->
    <div class="page-heading">
      <div>
        <div class="breadcrumb">Quản lý / Bình luận</div>
        <h1>Quản lý bình luận</h1>
        <p>Quản lý đánh giá sản phẩm và phản hồi khách hàng.</p>
      </div>

      <button class="btn-primary" :disabled="loading" @click="loadReviews">
        <i class="fa-solid fa-rotate-right" :class="{ 'fa-spin': loading }"></i>
        Làm mới
      </button>
    </div>

    <!-- Thống kê từ database -->
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-icon blue"><i class="fa-regular fa-comments"></i></div>
        <div>
          <div class="stat-label">Tổng đánh giá</div>
          <div class="stat-number">{{ reviews.length }}</div>
          <div class="stat-note">Tất cả đánh giá trong hệ thống</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon green"><i class="fa-regular fa-eye"></i></div>
        <div>
          <div class="stat-label">Đang hiển thị</div>
          <div class="stat-number">{{ visibleCount }}</div>
          <div class="stat-note">Khách hàng có thể xem</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon orange"><i class="fa-regular fa-eye-slash"></i></div>
        <div>
          <div class="stat-label">Đang ẩn</div>
          <div class="stat-number">{{ hiddenCount }}</div>
          <div class="stat-note">Không hiển thị ở trang sản phẩm</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon purple"><i class="fa-solid fa-star"></i></div>
        <div>
          <div class="stat-label">Đánh giá thấp</div>
          <div class="stat-number">{{ lowRatingCount }}</div>
          <div class="stat-note">Từ 1 đến 2 sao</div>
        </div>
      </div>
    </div>

    <!-- Danh sách -->
    <section class="content-card">
      <div class="list-heading">
        <div>
          <h2>Danh sách đánh giá</h2>
          <p>Tìm kiếm khách hàng, sản phẩm và quản lý trạng thái.</p>
        </div>

        <span class="result-count">{{ filteredReviews.length }} kết quả</span>
      </div>

      <!-- Tìm kiếm / lọc -->
      <div class="filter-bar">
        <div class="search-box">
          <i class="fa-solid fa-magnifying-glass search-icon"></i>
          <input
            v-model.trim="keyword"
            type="text"
            placeholder="Tìm khách hàng, email, tên sản phẩm..."
          />
          <button v-if="keyword" class="clear-search" @click="keyword = ''">
            ×
          </button>
        </div>

        <div class="filter-tabs">
          <button
            v-for="option in filterOptions"
            :key="option.value"
            :class="{ active: statusFilter === option.value }"
            @click="statusFilter = option.value"
          >
            {{ option.label }}
          </button>
        </div>
      </div>

      <!-- Đang tải -->
      <div v-if="loading" class="state-box">
        <i class="fa-solid fa-spinner fa-spin"></i>
        <strong>Đang tải đánh giá từ database...</strong>
      </div>

      <!-- Lỗi API -->
      <div v-else-if="error" class="state-box error-state">
        <i class="fa-solid fa-circle-exclamation"></i>
        <strong>{{ error }}</strong>
        <button class="btn-secondary" @click="loadReviews">Thử lại</button>
      </div>

      <!-- Không có dữ liệu -->
      <div v-else-if="filteredReviews.length === 0" class="state-box">
        <i class="fa-regular fa-comment-dots empty-icon"></i>
        <strong>
          {{ reviews.length ? 'Không tìm thấy đánh giá phù hợp' : 'Chưa có đánh giá nào' }}
        </strong>
        <span>
          {{ reviews.length
            ? 'Thử đổi từ khóa hoặc bộ lọc.'
            : 'Đánh giá của khách hàng sẽ xuất hiện tại đây sau khi được lưu vào database.' }}
        </span>
        <button v-if="keyword || statusFilter !== 'all'" class="btn-secondary" @click="resetFilters">
          Xóa bộ lọc
        </button>
      </div>

      <!-- Bảng dữ liệu thật -->
      <div v-else class="table-wrapper">
        <table>
          <thead>
            <tr>
              <th>KHÁCH HÀNG</th>
              <th>SẢN PHẨM</th>
              <th>NỘI DUNG ĐÁNH GIÁ</th>
              <th class="center">SỐ SAO</th>
              <th>NGÀY ĐÁNH GIÁ</th>
              <th>TRẠNG THÁI</th>
              <th class="center">THAO TÁC</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="item in filteredReviews" :key="item.id">
              <td>
                <div class="customer-cell">
                  <div class="customer-avatar">{{ initials(item.customerName) }}</div>
                  <div>
                    <div class="customer-name">{{ item.customerName }}</div>
                    <div class="customer-email">{{ item.email || 'Chưa có email' }}</div>
                    <div class="customer-code">Mã khách: #{{ item.customerId }}</div>
                  </div>
                </div>
              </td>

              <td>
                <div class="product-cell">
                  <span class="product-code">Mã SP: #{{ item.productId }}</span>
                  <span class="product-name">{{ item.productName }}</span>
                </div>
              </td>

              <td class="content-cell">
                <p>{{ item.content || 'Khách hàng không để lại nội dung.' }}</p>
                <button class="text-action" @click="viewReview(item)">
                  Xem nội dung đầy đủ
                </button>
              </td>

              <td class="center">
                <span class="rating-pill">
                  <i class="fa-solid fa-star"></i>
                  {{ item.stars }}<small>/5</small>
                </span>
              </td>

              <td class="date-cell">{{ item.date }}</td>

              <td>
                <span class="status-badge" :class="item.visible ? 'status-visible' : 'status-hidden'">
                  <span class="status-dot"></span>
                  {{ item.visible ? 'Hiển thị' : 'Đang ẩn' }}
                </span>
              </td>

              <td>
                <div class="action-group">
                  <button class="action-btn view-btn" title="Xem chi tiết" @click="viewReview(item)">
                    <i class="fa-regular fa-eye"></i>
                  </button>

                  <button
                    class="action-btn toggle-btn"
                    :disabled="busyId === item.id"
                    :title="item.visible ? 'Ẩn bình luận' : 'Hiển thị bình luận'"
                    @click="toggleStatus(item)"
                  >
                    <i
                      :class="item.visible
                        ? 'fa-regular fa-eye-slash'
                        : 'fa-regular fa-eye'"
                    ></i>
                  </button>

                  <button
                    class="action-btn delete-btn"
                    :disabled="busyId === item.id"
                    title="Xóa bình luận"
                    @click="askDelete(item)"
                  >
                    <i class="fa-regular fa-trash-can"></i>
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div v-if="!loading && !error" class="table-footer">
        <span>
          Đang hiển thị <strong>{{ filteredReviews.length }}</strong>
          / {{ reviews.length }} đánh giá
        </span>
        <span class="database-note">
          <i class="fa-solid fa-database"></i>
          Dữ liệu từ database
        </span>
      </div>
    </section>

    <!-- Popup chi tiết -->
    <div v-if="selectedReview" class="modal-overlay" @click.self="selectedReview = null">
      <section class="modal-card">
        <div class="modal-heading">
          <div>
            <div class="eyebrow">CHI TIẾT ĐÁNH GIÁ #{{ selectedReview.id }}</div>
            <h2>Nội dung bình luận</h2>
            <p>Thông tin được lấy từ hệ thống TECHSHOP.</p>
          </div>
          <button class="modal-close" @click="selectedReview = null">×</button>
        </div>

        <div class="detail-customer">
          <div class="customer-avatar large">{{ initials(selectedReview.customerName) }}</div>
          <div class="detail-customer-info">
            <strong>{{ selectedReview.customerName }}</strong>
            <span>{{ selectedReview.email || 'Chưa có email' }}</span>
            <small>Mã khách hàng: #{{ selectedReview.customerId }}</small>
          </div>
          <span class="status-badge" :class="selectedReview.visible ? 'status-visible' : 'status-hidden'">
            <span class="status-dot"></span>
            {{ selectedReview.visible ? 'Hiển thị' : 'Đang ẩn' }}
          </span>
        </div>

        <div class="detail-product">
          <div class="detail-product-icon"><i class="fa-solid fa-box"></i></div>
          <div>
            <small>SẢN PHẨM ĐƯỢC ĐÁNH GIÁ</small>
            <strong>{{ selectedReview.productName }}</strong>
            <span>Mã sản phẩm: #{{ selectedReview.productId }}</span>
          </div>
        </div>

        <div class="rating-detail">
          <div class="detail-stars">
            <i
              v-for="n in 5"
              :key="n"
              class="fa-solid fa-star"
              :class="n <= selectedReview.stars ? 'star-on' : 'star-off'"
            ></i>
          </div>
          <strong>{{ selectedReview.stars }}/5 sao</strong>
          <span>{{ selectedReview.date }}</span>
        </div>

        <div class="comment-detail">
          <label>NỘI DUNG KHÁCH HÀNG</label>
          <p>{{ selectedReview.content || 'Khách hàng không để lại nội dung.' }}</p>
        </div>

        <div class="modal-actions">
          <button class="btn-secondary" @click="selectedReview = null">Đóng</button>
          <button class="btn-primary" :disabled="busyId === selectedReview.id" @click="toggleSelectedStatus">
            <i :class="selectedReview.visible ? 'fa-regular fa-eye-slash' : 'fa-regular fa-eye'"></i>
            {{ selectedReview.visible ? 'Ẩn bình luận' : 'Hiển thị bình luận' }}
          </button>
        </div>
      </section>
    </div>

    <!-- Xác nhận xóa -->
    <div v-if="reviewToDelete" class="modal-overlay" @click.self="reviewToDelete = null">
      <section class="modal-card confirm-modal">
        <div class="confirm-icon"><i class="fa-regular fa-trash-can"></i></div>
        <h2>Xóa bình luận?</h2>
        <p>
          Bạn chắc chắn muốn xóa đánh giá của
          <strong>{{ reviewToDelete.customerName }}</strong> cho sản phẩm
          <strong>{{ reviewToDelete.productName }}</strong>?
          Thao tác này sẽ xóa dữ liệu trong database.
        </p>

        <div class="modal-actions centered-actions">
          <button class="btn-secondary" :disabled="deleting" @click="reviewToDelete = null">
            Hủy
          </button>
          <button class="btn-danger" :disabled="deleting" @click="deleteReview">
            <i v-if="deleting" class="fa-solid fa-spinner fa-spin"></i>
            {{ deleting ? 'Đang xóa...' : 'Xác nhận xóa' }}
          </button>
        </div>
      </section>
    </div>

    <!-- Thông báo -->
    <transition name="toast">
      <div v-if="toast" class="toast-message" :class="{ 'toast-error': toastType === 'error' }">
        <span>
          <i :class="toastType === 'error'
            ? 'fa-solid fa-circle-exclamation'
            : 'fa-solid fa-check'"></i>
        </span>
        {{ toast }}
      </div>
    </transition>
  </div>
</template>

<script>
import axios from "axios";

const API = "http://127.0.0.1:8000/api";

export default {
  name: "QuanLyBinhLuan",

  data() {
    return {
      reviews: [],
      loading: false,
      error: "",
      keyword: "",
      statusFilter: "all",
      selectedReview: null,
      reviewToDelete: null,
      busyId: null,
      deleting: false,
      toast: "",
      toastType: "success",
      toastTimer: null,

      filterOptions: [
        { label: "Tất cả", value: "all" },
        { label: "Hiển thị", value: "visible" },
        { label: "Đang ẩn", value: "hidden" }
      ]
    };
  },

  computed: {
    visibleCount() {
      return this.reviews.filter(item => item.visible).length;
    },

    hiddenCount() {
      return this.reviews.filter(item => !item.visible).length;
    },

    lowRatingCount() {
      return this.reviews.filter(item => item.stars <= 2).length;
    },

    filteredReviews() {
      const keyword = this.removeDiacritics(this.keyword.toLowerCase());

      return this.reviews.filter(item => {
        const searchableText = this.removeDiacritics(
          [
            item.id,
            item.customerId,
            item.customerName,
            item.email,
            item.productId,
            item.productName,
            item.content
          ].join(" ").toLowerCase()
        );

        const matchKeyword = !keyword || searchableText.includes(keyword);

        const matchStatus =
          this.statusFilter === "all" ||
          (this.statusFilter === "visible" && item.visible) ||
          (this.statusFilter === "hidden" && !item.visible);

        return matchKeyword && matchStatus;
      });
    }
  },

  mounted() {
    this.loadReviews();
  },

  beforeUnmount() {
    clearTimeout(this.toastTimer);
  },

  methods: {
    getConfig() {
      const token =
        localStorage.getItem("admin_token") ||
        localStorage.getItem("adminToken") ||
        localStorage.getItem("token");

      if (!token) {
        throw new Error(
          "Không tìm thấy token quản trị. Vui lòng đăng nhập lại trang Admin."
        );
      }

      return {
        headers: {
          Authorization: `Bearer ${token}`,
          Accept: "application/json"
        }
      };
    },

    removeDiacritics(value) {
      return String(value || "")
        .normalize("NFD")
        .replace(/[\u0300-\u036f]/g, "")
        .replace(/đ/g, "d")
        .replace(/Đ/g, "D");
    },

    initials(name) {
      const parts = String(name || "").trim().split(/\s+/).filter(Boolean);

      if (parts.length === 0) return "KH";
      if (parts.length === 1) return parts[0].slice(0, 2).toUpperCase();

      return (parts[0][0] + parts[parts.length - 1][0]).toUpperCase();
    },

    formatDate(value) {
      if (!value) return "—";

      const date = new Date(value);

      if (Number.isNaN(date.getTime())) {
        return String(value);
      }

      return date.toLocaleString("vi-VN", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
        hour: "2-digit",
        minute: "2-digit"
      });
    },

    normalizeReview(item) {
      const user = item.nguoi_dung || item.nguoiDung || {};
      const product = item.san_pham || item.sanPham || {};

      const status = item.TrangThai;
      const visible =
        status === true ||
        status === 1 ||
        status === "1";

      return {
        id: item.MaDG,
        customerId: item.MaND,
        customerName: user.HoTen || `Khách hàng #${item.MaND}`,
        email: user.Email || "",
        productId: item.MaSP,
        productName: product.TenSP || `Sản phẩm #${item.MaSP}`,
        stars: Number(item.SoSao || 0),
        content: item.NoiDung || "",
        date: this.formatDate(item.NgayDG),
        visible
      };
    },

    async loadReviews() {
      this.loading = true;
      this.error = "";

      try {
        const response = await axios.get(
          `${API}/admin/danh-gia/get-data`,
          this.getConfig()
        );

        if (response.data.status !== true) {
          throw new Error(
            response.data.message || "Không lấy được danh sách đánh giá."
          );
        }

        const data = Array.isArray(response.data.data)
          ? response.data.data
          : [];

        this.reviews = data.map(item => this.normalizeReview(item));
      } catch (error) {
        console.error("Lỗi lấy danh sách đánh giá:", error.response?.data || error);

        this.error =
          error.response?.data?.message ||
          error.message ||
          "Không thể tải dữ liệu đánh giá.";

        this.showToast(this.error, "error");
      } finally {
        this.loading = false;
      }
    },

    viewReview(item) {
      this.selectedReview = { ...item };
    },

    async toggleStatus(item) {
      if (this.busyId === item.id) return;

      const newStatus = item.visible ? 0 : 1;
      this.busyId = item.id;

      try {
        const response = await axios.put(
          `${API}/admin/danh-gia/update-status`,
          {
            MaDG: item.id,
            TrangThai: newStatus
          },
          this.getConfig()
        );

        if (response.data.status !== true) {
          throw new Error(
            response.data.message || "Không thể cập nhật trạng thái."
          );
        }

        const updated = {
          ...item,
          visible: newStatus === 1
        };

        this.reviews = this.reviews.map(row =>
          row.id === item.id ? updated : row
        );

        if (this.selectedReview?.id === item.id) {
          this.selectedReview = { ...updated };
        }

        if (this.reviewToDelete?.id === item.id) {
          this.reviewToDelete = { ...updated };
        }

        this.showToast(
          newStatus === 1
            ? "Đã hiển thị bình luận."
            : "Đã ẩn bình luận."
        );
      } catch (error) {
        console.error("Lỗi đổi trạng thái:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
          error.message ||
          "Không thể cập nhật trạng thái.",
          "error"
        );
      } finally {
        this.busyId = null;
      }
    },

    async toggleSelectedStatus() {
      if (!this.selectedReview) return;
      await this.toggleStatus(this.selectedReview);
    },

    askDelete(item) {
      this.reviewToDelete = { ...item };
    },

    async deleteReview() {
      if (!this.reviewToDelete || this.deleting) return;

      this.deleting = true;

      try {
        const response = await axios.delete(
          `${API}/admin/danh-gia/delete`,
          {
            ...this.getConfig(),
            data: {
              MaDG: this.reviewToDelete.id
            }
          }
        );

        if (response.data.status !== true) {
          throw new Error(
            response.data.message || "Xóa bình luận thất bại."
          );
        }

        const deletedId = this.reviewToDelete.id;

        this.reviews = this.reviews.filter(item => item.id !== deletedId);

        if (this.selectedReview?.id === deletedId) {
          this.selectedReview = null;
        }

        this.reviewToDelete = null;
        this.showToast("Đã xóa bình luận khỏi database.");
      } catch (error) {
        console.error("Lỗi xóa bình luận:", error.response?.data || error);

        this.showToast(
          error.response?.data?.message ||
          error.message ||
          "Không thể xóa bình luận.",
          "error"
        );
      } finally {
        this.deleting = false;
      }
    },

    resetFilters() {
      this.keyword = "";
      this.statusFilter = "all";
    },

    showToast(message, type = "success") {
      this.toast = message;
      this.toastType = type;

      clearTimeout(this.toastTimer);

      this.toastTimer = setTimeout(() => {
        this.toast = "";
      }, 3500);
    }
  }
};
</script>
<style scoped>
/* =========================================================
   BẢNG MÀU & BIẾN DÙNG CHUNG
   ========================================================= */
.comment-page {
  --primary: #4f46e5;
  --primary-2: #2563eb;
  --accent: #06b6d4;
  --navy: #0f172a;
  --text: #1e293b;
  --muted: #64748b;
  --line: #e2e8f0;
  --soft: #f1f5f9;
  --grad-primary: linear-gradient(135deg, #4f46e5 0%, #2563eb 55%, #06b6d4 100%);
  --grad-danger: linear-gradient(135deg, #f43f5e 0%, #e11d48 100%);
  --grad-success: linear-gradient(135deg, #34d399 0%, #059669 100%);
  --grad-warm: linear-gradient(135deg, #fbbf24 0%, #f97316 100%);
  --shadow-sm: 0 2px 8px rgba(15, 23, 42, 0.06);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  width: 100%;
  min-width: 0;
  padding: 4px 2px 24px;
  color: var(--text);
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.comment-page * {
  box-sizing: border-box;
}

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(16px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes rowIn {
  from { opacity: 0; transform: translateX(-10px); }
  to { opacity: 1; transform: translateX(0); }
}

@keyframes pulseDot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.35); }
}

@keyframes shine {
  from { transform: translateX(-120%) skewX(-20deg); }
  to { transform: translateX(260%) skewX(-20deg); }
}

@keyframes overlayIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes modalIn {
  from { opacity: 0; transform: translateY(28px) scale(0.95); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes starPop {
  0% { transform: scale(0.4) rotate(-30deg); opacity: 0; }
  70% { transform: scale(1.25) rotate(8deg); opacity: 1; }
  100% { transform: scale(1) rotate(0); opacity: 1; }
}

.page-heading { animation: fadeUp 0.5s var(--ease) both; }

.stat-card { animation: fadeUp 0.55s var(--ease) both; }
.stat-card:nth-child(1) { animation-delay: 0.05s; }
.stat-card:nth-child(2) { animation-delay: 0.12s; }
.stat-card:nth-child(3) { animation-delay: 0.19s; }
.stat-card:nth-child(4) { animation-delay: 0.26s; }

.content-card { animation: fadeUp 0.6s var(--ease) 0.3s both; }

tbody tr { animation: rowIn 0.4s var(--ease) both; }
tbody tr:nth-child(1) { animation-delay: 0.04s; }
tbody tr:nth-child(2) { animation-delay: 0.08s; }
tbody tr:nth-child(3) { animation-delay: 0.12s; }
tbody tr:nth-child(4) { animation-delay: 0.16s; }
tbody tr:nth-child(5) { animation-delay: 0.2s; }
tbody tr:nth-child(6) { animation-delay: 0.24s; }
tbody tr:nth-child(7) { animation-delay: 0.28s; }
tbody tr:nth-child(8) { animation-delay: 0.32s; }

/* =========================================================
   TIÊU ĐỀ
   ========================================================= */
.page-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 26px;
}

.breadcrumb {
  display: inline-flex;
  margin-bottom: 10px;
  padding: 4px 12px;
  border-radius: 999px;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 12px;
  font-weight: 700;
}

.page-heading h1 {
  margin: 0;
  font-size: clamp(25px, 2.2vw, 30px);
  font-weight: 800;
  letter-spacing: -0.4px;
  background: linear-gradient(90deg, #0f172a 0%, #4f46e5 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.page-heading p {
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 14px;
}

/* =========================================================
   NÚT
   ========================================================= */
.btn-primary,
.btn-secondary,
.btn-danger {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
  min-height: 42px;
  padding: 0 18px;
  border: 0;
  border-radius: 11px;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s;
}

.btn-primary {
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
}

.btn-danger {
  color: #fff;
  background: var(--grad-danger);
  box-shadow: 0 8px 18px rgba(244, 63, 94, 0.3);
}

.btn-primary::after,
.btn-danger::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 40%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.35), transparent);
  transform: translateX(-120%) skewX(-20deg);
  pointer-events: none;
}

.btn-primary:hover:not(:disabled),
.btn-danger:hover:not(:disabled) {
  transform: translateY(-2px);
}

.btn-primary:hover:not(:disabled) {
  background-position: 100% 50%;
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.42);
}

.btn-danger:hover:not(:disabled) {
  box-shadow: 0 12px 26px rgba(244, 63, 94, 0.42);
}

.btn-primary:hover:not(:disabled)::after,
.btn-danger:hover:not(:disabled)::after {
  animation: shine 0.8s var(--ease);
}

.btn-primary:active:not(:disabled),
.btn-danger:active:not(:disabled) {
  transform: scale(0.97);
}

.btn-secondary {
  border: 1.5px solid var(--line);
  color: #475569;
  background: #fff;
}

.btn-secondary:hover:not(:disabled) {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.btn-primary:disabled,
.btn-danger:disabled,
.btn-secondary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* =========================================================
   THẺ THỐNG KÊ
   ========================================================= */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 18px;
  margin-bottom: 24px;
}

.stat-card {
  --tone: #4f46e5;
  --tone-soft: rgba(79, 70, 229, 0.1);
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  gap: 16px;
  min-width: 0;
  min-height: 112px;
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 16px;
  background: #fff;
  box-shadow: var(--shadow-sm);
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease),
    border-color 0.3s;
}

.stat-card::before {
  content: "";
  position: absolute;
  top: -38px;
  right: -38px;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background: var(--tone-soft);
  transition: transform 0.5s var(--ease);
}

.stat-card::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 4px;
  background: var(--tone);
  transform: scaleX(0.28);
  transform-origin: left;
  transition: transform 0.45s var(--ease);
}

.stat-card:nth-child(1) { --tone: #4f46e5; --tone-soft: rgba(79, 70, 229, 0.1); }
.stat-card:nth-child(2) { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); }
.stat-card:nth-child(3) { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); }
.stat-card:nth-child(4) { --tone: #a855f7; --tone-soft: rgba(168, 85, 247, 0.12); }

.stat-card:hover {
  transform: translateY(-6px);
  border-color: transparent;
  box-shadow: 0 16px 36px rgba(15, 23, 42, 0.12);
}

.stat-card:hover::before { transform: scale(1.5); }
.stat-card:hover::after { transform: scaleX(1); }

.stat-card > div:last-child {
  position: relative;
  z-index: 1;
  min-width: 0;
}

.stat-icon {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 54px;
  height: 54px;
  flex-shrink: 0;
  border-radius: 16px;
  color: #fff;
  font-size: 21px;
  transition: transform 0.35s var(--ease);
}

.stat-card:hover .stat-icon { transform: rotate(-8deg) scale(1.1); }

.blue {
  color: #fff;
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.35);
}

.green {
  color: #fff;
  background: linear-gradient(135deg, #34d399, #059669);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.35);
}

.orange {
  color: #fff;
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38);
}

.purple {
  color: #fff;
  background: linear-gradient(135deg, #c084fc, #9333ea);
  box-shadow: 0 8px 16px rgba(168, 85, 247, 0.35);
}

.stat-label {
  margin-bottom: 6px;
  color: var(--muted);
  font-size: 12px;
  font-weight: 600;
}

.stat-number {
  color: var(--navy);
  font-size: 28px;
  font-weight: 800;
  line-height: 1.1;
}

.stat-note {
  margin-top: 6px;
  color: #94a3b8;
  font-size: 11px;
}

/* =========================================================
   KHUNG DANH SÁCH
   ========================================================= */
.content-card {
  overflow: hidden;
  border: 1px solid var(--line);
  border-radius: 18px;
  background: #fff;
  box-shadow: 0 8px 30px rgba(15, 23, 42, 0.06);
}

.list-heading {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 15px;
  padding: 24px 26px 20px;
}

.list-heading h2 {
  margin: 0;
  color: var(--navy);
  font-size: 18px;
  font-weight: 800;
}

.list-heading p {
  margin: 6px 0 0;
  color: var(--muted);
  font-size: 12.5px;
}

.result-count {
  padding: 8px 14px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 12px;
  font-weight: 700;
  white-space: nowrap;
}

/* =========================================================
   TÌM KIẾM / LỌC
   ========================================================= */
.filter-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 16px;
  padding: 16px 26px;
  border-top: 1px solid var(--line);
  border-bottom: 1px solid var(--line);
  background: #f8fafc;
}

.search-box {
  display: flex;
  align-items: center;
  width: min(420px, 100%);
  min-height: 46px;
  border: 1.5px solid var(--line);
  border-radius: 12px;
  color: var(--muted);
  background: #fff;
  transition: all 0.25s var(--ease);
}

.search-box:hover { border-color: #c7d2fe; }

.search-box:focus-within {
  color: var(--primary);
  border-color: var(--primary);
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

.search-icon {
  margin: 0 10px 0 14px;
  font-size: 13px;
}

.search-box input {
  width: 100%;
  min-width: 0;
  height: 42px;
  padding: 0 8px 0 0;
  border: 0;
  outline: 0;
  color: var(--navy);
  background: transparent;
  font-size: 13px;
}

.search-box input::placeholder { color: #94a3b8; }

.clear-search {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  margin-right: 10px;
  padding: 0;
  border: 0;
  border-radius: 50%;
  color: #64748b;
  background: #e2e8f0;
  font-size: 17px;
  line-height: 1;
  cursor: pointer;
  transition: all 0.2s;
}

.clear-search:hover {
  color: #fff;
  background: #f43f5e;
  transform: rotate(90deg);
}

.filter-tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 4px;
  padding: 4px;
  border: 1px solid var(--line);
  border-radius: 12px;
  background: var(--soft);
}

.filter-tabs button {
  padding: 9px 16px;
  border: 0;
  border-radius: 9px;
  color: var(--muted);
  background: transparent;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.25s var(--ease);
}

.filter-tabs button:hover:not(.active) {
  color: var(--primary);
  background: #e0e7ff;
}

.filter-tabs button.active {
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.35);
}

/* =========================================================
   BẢNG
   ========================================================= */
.table-wrapper {
  width: 100%;
  overflow-x: auto;
}

table {
  width: 100%;
  min-width: 1100px;
  border-collapse: separate;
  border-spacing: 0;
  text-align: left;
}

thead th {
  padding: 15px;
  color: #3730a3;
  background: linear-gradient(180deg, #eef2ff, #e0e7ff);
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.4px;
  white-space: nowrap;
}

thead th:first-child,
tbody td:first-child { padding-left: 26px; }

tbody td {
  padding: 16px 15px;
  border-bottom: 1px solid #eef2f7;
  color: #475569;
  font-size: 12px;
  vertical-align: middle;
  transition: background 0.25s;
}

tbody tr:hover td { background: #f5f7ff; }
tbody tr:hover td:first-child { box-shadow: inset 4px 0 0 var(--primary); }
tbody tr:last-child td { border-bottom: 0; }

.center { text-align: center !important; }

/* khách hàng */
.customer-cell {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 190px;
}

.customer-avatar {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 42px;
  height: 42px;
  flex-shrink: 0;
  border: 2px solid #fff;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.18);
  font-size: 13px;
  font-weight: 800;
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease);
}

tbody tr:hover .customer-avatar {
  transform: scale(1.1);
  box-shadow: 0 0 0 2px var(--primary), 0 8px 16px rgba(79, 70, 229, 0.3);
}

.customer-avatar.large {
  width: 52px;
  height: 52px;
  font-size: 15px;
}

.customer-name {
  color: var(--navy);
  font-weight: 700;
  white-space: nowrap;
  transition: color 0.2s;
}

tbody tr:hover .customer-name { color: var(--primary); }

.customer-email,
.customer-code {
  margin-top: 4px;
  color: #94a3b8;
  font-size: 10.5px;
  white-space: nowrap;
}

/* sản phẩm */
.product-cell {
  display: flex;
  flex-direction: column;
  gap: 6px;
  min-width: 140px;
}

.product-code {
  align-self: flex-start;
  padding: 3px 8px;
  border-radius: 6px;
  color: #4338ca;
  background: #e0e7ff;
  font-size: 10px;
  font-weight: 800;
}

.product-name {
  max-width: 190px;
  color: #334155;
  font-size: 12px;
  font-weight: 700;
  line-height: 1.45;
}

/* nội dung */
.content-cell {
  min-width: 220px;
  max-width: 300px;
}

.content-cell p {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  margin: 0;
  color: #475569;
  font-size: 12px;
  line-height: 1.6;
}

.text-action {
  margin-top: 6px;
  padding: 0;
  border: 0;
  color: var(--primary);
  background: transparent;
  font-size: 11px;
  font-weight: 700;
  cursor: pointer;
  transition: letter-spacing 0.2s, color 0.2s;
}

.text-action:hover {
  color: var(--primary-2);
  text-decoration: underline;
}

/* số sao */
.rating-pill {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 7px 12px;
  border-radius: 999px;
  color: #b45309;
  background: linear-gradient(135deg, #fef3c7, #fde68a);
  box-shadow: 0 4px 10px rgba(245, 158, 11, 0.22);
  font-size: 12px;
  font-weight: 800;
  white-space: nowrap;
}

.rating-pill i {
  color: #f59e0b;
  transition: transform 0.4s var(--ease);
}

tbody tr:hover .rating-pill i { transform: rotate(72deg) scale(1.25); }

.rating-pill small {
  color: #a16207;
  font-size: 10px;
}

.date-cell {
  min-width: 135px;
  color: var(--muted);
  white-space: nowrap;
}

/* trạng thái */
.status-badge {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 6px 12px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.status-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: currentColor;
}

.status-visible {
  color: #047857;
  background: #d1fae5;
}

.status-visible .status-dot { animation: pulseDot 1.8s ease-in-out infinite; }

.status-hidden {
  color: #475569;
  background: #e2e8f0;
}

/* thao tác */
.action-group {
  display: flex;
  justify-content: center;
  flex-wrap: nowrap;
  gap: 7px;
  min-width: 120px;
}

.action-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 34px;
  height: 34px;
  border: 0;
  border-radius: 10px;
  color: #fff;
  font-size: 13px;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease);
}

.view-btn {
  background: linear-gradient(135deg, #6366f1, #2563eb);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.3);
}

.toggle-btn {
  background: var(--grad-warm);
  box-shadow: 0 5px 12px rgba(245, 158, 11, 0.32);
}

.delete-btn {
  background: var(--grad-danger);
  box-shadow: 0 5px 12px rgba(244, 63, 94, 0.3);
}

.action-btn:hover:not(:disabled) {
  transform: translateY(-3px) scale(1.08);
}

.view-btn:hover:not(:disabled) { box-shadow: 0 10px 20px rgba(79, 70, 229, 0.45); }
.toggle-btn:hover:not(:disabled) { box-shadow: 0 10px 20px rgba(245, 158, 11, 0.45); }
.delete-btn:hover:not(:disabled) { box-shadow: 0 10px 20px rgba(244, 63, 94, 0.45); }

.action-btn:active:not(:disabled) { transform: scale(0.92); }

.action-btn:disabled {
  opacity: 0.45;
  cursor: not-allowed;
  box-shadow: none;
  filter: grayscale(0.4);
}

/* chân bảng */
.table-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
  padding: 15px 26px;
  border-top: 1px solid var(--line);
  background: #f8fafc;
  color: var(--muted);
  font-size: 12px;
}

.table-footer strong { color: var(--primary); }

.database-note {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 5px 12px;
  border-radius: 999px;
  color: #047857;
  background: #d1fae5;
  font-weight: 700;
}

/* =========================================================
   TRẠNG THÁI TẢI / LỖI / TRỐNG
   ========================================================= */
.state-box {
  display: flex;
  align-items: center;
  flex-direction: column;
  gap: 12px;
  padding: 52px 20px;
  color: var(--muted);
  text-align: center;
  font-size: 13px;
}

.state-box > i {
  font-size: 28px;
  color: var(--primary);
}

.state-box strong {
  color: var(--navy);
  font-size: 14px;
  font-weight: 800;
}

.empty-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 74px;
  height: 74px;
  border-radius: 50%;
  background: #e0e7ff;
  font-size: 30px !important;
}

.error-state { background: linear-gradient(180deg, #fff1f2, #fff); }
.error-state > i { color: #f43f5e; }
.error-state strong { color: #be123c; }

/* =========================================================
   MODAL
   ========================================================= */
.modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 1100;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  background: rgba(15, 23, 42, 0.55);
  backdrop-filter: blur(4px);
  animation: overlayIn 0.25s ease both;
}

.modal-card {
  width: min(100%, 580px);
  max-height: 90vh;
  overflow-y: auto;
  padding: 0 26px 26px;
  border-radius: 20px;
  background: #fff;
  box-shadow: 0 30px 80px rgba(15, 23, 42, 0.3);
  animation: modalIn 0.4s var(--ease) both;
}

.modal-heading {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  margin: 0 -26px;
  padding: 24px 26px;
  color: #fff;
  background: var(--grad-primary);
}

.eyebrow {
  display: inline-block;
  margin-bottom: 8px;
  padding: 4px 10px;
  border-radius: 999px;
  color: #fff;
  background: rgba(255, 255, 255, 0.22);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 0.4px;
}

.modal-heading h2 {
  margin: 0;
  color: #fff;
  font-size: 21px;
  font-weight: 800;
}

.modal-heading p {
  margin: 5px 0 0;
  color: rgba(255, 255, 255, 0.85);
  font-size: 12.5px;
}

.modal-close {
  flex-shrink: 0;
  width: 34px;
  height: 34px;
  border: 0;
  border-radius: 50%;
  color: #fff;
  background: rgba(255, 255, 255, 0.2);
  font-size: 22px;
  line-height: 1;
  cursor: pointer;
  transition: transform 0.3s var(--ease), background 0.2s;
}

.modal-close:hover {
  background: rgba(255, 255, 255, 0.35);
  transform: rotate(90deg);
}

.detail-customer {
  display: flex;
  align-items: center;
  gap: 13px;
  padding: 20px 0;
}

.detail-customer-info {
  display: flex;
  flex: 1;
  flex-direction: column;
  gap: 5px;
  min-width: 0;
}

.detail-customer-info strong {
  color: var(--navy);
  font-size: 14px;
  font-weight: 800;
}

.detail-customer-info span,
.detail-customer-info small {
  color: #94a3b8;
  font-size: 11.5px;
  overflow-wrap: anywhere;
}

.detail-product {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 15px;
  border: 1px solid #c7d2fe;
  border-radius: 14px;
  background: linear-gradient(135deg, #eef2ff, #f8faff);
}

.detail-product-icon {
  display: grid;
  place-items: center;
  width: 46px;
  height: 46px;
  border-radius: 13px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.3);
}

.detail-product div:last-child {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.detail-product small {
  color: var(--primary);
  font-size: 9px;
  font-weight: 800;
  letter-spacing: 0.3px;
}

.detail-product strong {
  color: var(--navy);
  font-size: 13px;
  font-weight: 800;
}

.detail-product span {
  color: #94a3b8;
  font-size: 11px;
}

.rating-detail {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 18px 0;
  border-bottom: 1px solid var(--line);
}

.detail-stars {
  display: flex;
  gap: 4px;
  font-size: 16px;
}

.star-on {
  color: #f59e0b;
  filter: drop-shadow(0 2px 4px rgba(245, 158, 11, 0.4));
  animation: starPop 0.5s var(--ease) both;
}

.detail-stars .star-on:nth-child(1) { animation-delay: 0.1s; }
.detail-stars .star-on:nth-child(2) { animation-delay: 0.18s; }
.detail-stars .star-on:nth-child(3) { animation-delay: 0.26s; }
.detail-stars .star-on:nth-child(4) { animation-delay: 0.34s; }
.detail-stars .star-on:nth-child(5) { animation-delay: 0.42s; }

.star-off { color: #e2e8f0; }

.rating-detail strong {
  color: #b45309;
  font-size: 13px;
  font-weight: 800;
}

.rating-detail span {
  margin-left: auto;
  color: #94a3b8;
  font-size: 11.5px;
}

.comment-detail { padding: 20px 0 5px; }

.comment-detail label {
  display: block;
  margin-bottom: 10px;
  color: var(--primary);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 0.4px;
}

.comment-detail p {
  margin: 0;
  padding: 16px;
  border: 1px solid var(--line);
  border-left: 4px solid var(--primary);
  border-radius: 12px;
  color: #334155;
  background: #f8fafc;
  font-size: 13px;
  line-height: 1.8;
  overflow-wrap: anywhere;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 9px;
  margin-top: 22px;
  padding-top: 18px;
  border-top: 1px solid var(--line);
}

/* xác nhận xóa */
.confirm-modal {
  width: min(100%, 440px);
  padding-top: 30px;
  text-align: center;
}

.confirm-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 68px;
  height: 68px;
  border-radius: 50%;
  color: #fff;
  background: var(--grad-danger);
  box-shadow: 0 10px 24px rgba(244, 63, 94, 0.4);
  font-size: 26px;
  animation: pulseDot 2s ease-in-out infinite;
}

.confirm-modal h2 {
  margin: 16px 0 0;
  color: var(--navy);
  font-size: 21px;
  font-weight: 800;
}

.confirm-modal p {
  color: var(--muted);
  font-size: 13px;
  line-height: 1.8;
}

.confirm-modal p strong { color: var(--navy); }

.centered-actions {
  justify-content: center;
  border-top: 0;
}

/* =========================================================
   THÔNG BÁO (TOAST)
   ========================================================= */
.toast-message {
  position: fixed;
  right: 24px;
  bottom: 24px;
  z-index: 1300;
  display: flex;
  align-items: center;
  gap: 11px;
  max-width: calc(100vw - 32px);
  padding: 14px 20px 14px 14px;
  border-radius: 14px;
  color: #fff;
  background: linear-gradient(135deg, #059669, #10b981);
  box-shadow: 0 14px 34px rgba(5, 150, 105, 0.4);
  font-size: 13px;
  font-weight: 600;
}

.toast-message span {
  display: grid;
  place-items: center;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  color: #059669;
  background: #fff;
}

.toast-message.toast-error {
  background: var(--grad-danger);
  box-shadow: 0 14px 34px rgba(244, 63, 94, 0.4);
}

.toast-message.toast-error span { color: #e11d48; }

.toast-enter-active,
.toast-leave-active {
  transition: opacity 0.3s var(--ease), transform 0.3s var(--ease);
}

.toast-enter-from,
.toast-leave-to {
  opacity: 0;
  transform: translateX(30px) scale(0.95);
}

/* thanh cuộn gọn */
.table-wrapper::-webkit-scrollbar { height: 8px; }
.table-wrapper::-webkit-scrollbar-thumb { border-radius: 8px; background: #c7d2fe; }
.table-wrapper::-webkit-scrollbar-thumb:hover { background: #818cf8; }

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1150px) {
  .stats-grid { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}

@media (max-width: 700px) {
  .page-heading { align-items: flex-start; flex-direction: column; }
  .page-heading h1 { font-size: 24px; }
  .stats-grid { gap: 10px; }
  .stat-card { align-items: flex-start; flex-direction: column; gap: 10px; padding: 14px; }
  .stat-icon { width: 42px; height: 42px; border-radius: 12px; font-size: 17px; }
  .stat-number { font-size: 23px; }
  .filter-bar { padding: 14px; }
  .search-box { width: 100%; }
  .list-heading { padding: 20px 15px; }
  .table-footer { align-items: flex-start; flex-direction: column; padding: 14px 15px; }
  .modal-card { padding: 0 18px 20px; }
  .modal-heading { margin: 0 -18px; padding: 20px 18px; }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .comment-page *,
  .comment-page *::before,
  .comment-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>