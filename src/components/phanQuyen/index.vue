
<template>
  <div class="user-page">
    <!-- TIÊU ĐỀ -->
    <div class="page-heading">
      <div>
        <div class="breadcrumb">Quản lý / Người dùng</div>
        <h1>Quản lý người dùng</h1>
        <p>Quản lý tài khoản, cấp vai trò quản trị và trạng thái hoạt động.</p>
      </div>

      <button class="btn-primary" :disabled="dangTai" @click="getData">
        <span class="refresh-icon">↻</span>
        Làm mới dữ liệu
      </button>
    </div>

    <!-- THỐNG KÊ -->
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-icon icon-blue">♙</div>
        <div>
          <div class="stat-label">Tổng tài khoản</div>
          <div class="stat-number">{{ danhSachNguoiDung.length }}</div>
          <div class="stat-note">Tất cả người dùng</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-red">♛</div>
        <div>
          <div class="stat-label">Đã cấp quyền quản trị</div>
          <div class="stat-number">{{ danhSachDaCapQuyen.length }}</div>
          <div class="stat-note">Quản lý và nhân viên</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-green">✓</div>
        <div>
          <div class="stat-label">Chưa cấp quyền quản trị</div>
          <div class="stat-number">{{ danhSachChuaCapQuyen.length }}</div>
          <div class="stat-note">Tài khoản khách hàng</div>
        </div>
      </div>

      <div class="stat-card">
        <div class="stat-icon icon-orange">▣</div>
        <div>
          <div class="stat-label">Tài khoản bị khóa</div>
          <div class="stat-number">{{ soTaiKhoanBiKhoa }}</div>
          <div class="stat-note">Không thể đăng nhập</div>
        </div>
      </div>
    </div>

    <!-- DANH SÁCH TÀI KHOẢN -->
    <section class="content-card">
      <div class="list-heading">
        <div>
          <h2>Danh sách tài khoản</h2>
          <p>
            Chọn danh sách để cấp quyền quản trị hoặc quản lý tài khoản
            đã được cấp quyền.
          </p>
        </div>

        <span class="result-count">
          {{ danhSachDangXem.length }} tài khoản
        </span>
      </div>

      <!-- HAI TAB -->
      <div class="tab-bar">
        <button
          class="tab-btn"
          :class="{ active: tabHienTai === 'da-cap-quyen' }"
          @click="tabHienTai = 'da-cap-quyen'"
        >
          Đã cấp quyền quản trị
          <span class="tab-count">
            {{ danhSachDaCapQuyen.length }}
          </span>
        </button>

        <button
          class="tab-btn"
          :class="{ active: tabHienTai === 'chua-cap-quyen' }"
          @click="tabHienTai = 'chua-cap-quyen'"
        >
          Chưa cấp quyền quản trị
          <span class="tab-count">
            {{ danhSachChuaCapQuyen.length }}
          </span>
        </button>
      </div>

      <!-- ĐANG TẢI -->
      <div v-if="dangTai" class="message-box">
        <span class="loading-spinner"></span>
        Đang tải danh sách người dùng...
      </div>

      <!-- LỖI -->
      <div v-else-if="loi" class="message-box error-message">
        <div>
          <strong>Không tải được dữ liệu</strong>
          <p>{{ loi }}</p>
        </div>

        <button class="btn-secondary" @click="getData">
          Thử lại
        </button>
      </div>

      <!-- BẢNG NGƯỜI DÙNG -->
      <div v-else class="table-wrapper">
        <table>
          <thead>
            <tr>
              <th class="code-column">MÃ ND</th>
              <th>THÔNG TIN NGƯỜI DÙNG</th>
              <th>SỐ ĐIỆN THOẠI</th>
              <th>ĐỊA CHỈ</th>
              <th>VAI TRÒ</th>
              <th>TRẠNG THÁI</th>
              <th class="text-center action-column">THAO TÁC</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="nguoiDung in danhSachDangXem"
              :key="nguoiDung.MaND"
            >
              <td>
                <span class="code-badge">
                  ND{{ String(nguoiDung.MaND).padStart(3, '0') }}
                </span>
              </td>

              <td>
  <div class="user-info">
    <!-- Hiển thị ảnh nếu có và ảnh chưa bị lỗi -->
    <img
      v-if="getAvatarUrl(nguoiDung) && !anhDaiDienLoi[nguoiDung.MaND]"
      class="user-avatar"
      :src="getAvatarUrl(nguoiDung)"
      :alt="nguoiDung.HoTen || 'Ảnh đại diện'"
      @error="anhLoi($event, nguoiDung.MaND)"
    />

    <!-- Không có ảnh hoặc ảnh bị lỗi thì hiển thị chữ cái đầu -->
    <div v-else class="user-avatar avatar-fallback">
      {{
        (nguoiDung.HoTen || '?')
          .trim()
          .charAt(0)
          .toUpperCase()
      }}
    </div>

    <!-- Thông tin người dùng -->
    <div class="user-text">
      <div class="user-name">
        {{ nguoiDung.HoTen || 'Chưa cập nhật' }}
      </div>

      <div class="user-email">
        {{ nguoiDung.Email || 'Chưa có email' }}
      </div>
    </div>
  </div>
</td>

              <td>
                {{ nguoiDung.SoDienThoai || '—' }}
              </td>

              <td class="address-cell">
                {{ nguoiDung.DiaChi || '—' }}
              </td>

              <td>
                <span
                  class="role-badge"
                  :class="getRoleClass(nguoiDung.MaQuyen)"
                >
                  {{ getTenQuyen(nguoiDung.MaQuyen) }}
                </span>
              </td>

              <td>
                <span
                  class="status-badge"
                  :class="
                    nguoiDung.TrangThai === 'HoatDong'
                      ? 'status-active'
                      : 'status-locked'
                  "
                >
                  <span class="status-dot"></span>

                  {{
                    nguoiDung.TrangThai === 'HoatDong'
                      ? 'Hoạt động'
                      : 'Đã khóa'
                  }}
                </span>
              </td>

              <!-- THAO TÁC -->
              <td class="text-center">
                <div class="actions-cell">

                  <!-- DANH SÁCH CHƯA CẤP QUYỀN -->
                  <template v-if="tabHienTai === 'chua-cap-quyen'">
                    <select
                      v-model.number="vaiTroDuocChon[nguoiDung.MaND]"
                      class="role-select"
                      :disabled="
                        nguoiDung.TrangThai !== 'HoatDong' ||
                        dangXuLy === nguoiDung.MaND
                      "
                      aria-label="Chọn vai trò để cấp quyền"
                    >
                      <option :value="1">Quản lý cửa hàng</option>
                      <option :value="2">Nhân viên bán hàng</option>
                      <option :value="3">Nhân viên kho</option>
                    </select>

                    <button
                      class="action-btn grant-btn"
                      :disabled="
                        nguoiDung.TrangThai !== 'HoatDong' ||
                        dangXuLy === nguoiDung.MaND
                      "
                      @click="capQuyen(nguoiDung)"
                    >
                      {{
                        dangXuLy === nguoiDung.MaND
                          ? 'Đang xử lý...'
                          : 'Cấp quyền'
                      }}
                    </button>
                  </template>

                  <!-- DANH SÁCH ĐÃ CẤP QUYỀN -->
                  <template v-else>
                    <select
                      v-model.number="vaiTroDuocChon[nguoiDung.MaND]"
                      class="role-select"
                      :disabled="dangXuLy === nguoiDung.MaND"
                      aria-label="Thay đổi vai trò"
                    >
                      <option :value="1">Quản lý cửa hàng</option>
                      <option :value="2">Nhân viên bán hàng</option>
                      <option :value="3">Nhân viên kho</option>
                    </select>

                    <button
                      class="action-btn change-btn"
                      :disabled="
                        dangXuLy === nguoiDung.MaND ||
                        Number(vaiTroDuocChon[nguoiDung.MaND]) ===
                          Number(nguoiDung.MaQuyen)
                      "
                      @click="capQuyen(nguoiDung)"
                    >
                      Đổi vai trò
                    </button>

                    <button
                      class="action-btn revoke-btn"
                      :disabled="dangXuLy === nguoiDung.MaND"
                      @click="thuHoiQuyen(nguoiDung)"
                    >
                      Thu hồi
                    </button>
                  </template>

                  <!-- KHÓA -->
                  <button
                    v-if="nguoiDung.TrangThai === 'HoatDong'"
                    class="action-btn lock-btn"
                    :disabled="dangXuLy === nguoiDung.MaND"
                    @click="khoaTaiKhoan(nguoiDung)"
                  >
                    Khóa
                  </button>

                  <!-- MỞ KHÓA -->
                  <button
                    v-else
                    class="action-btn unlock-btn"
                    :disabled="dangXuLy === nguoiDung.MaND"
                    @click="moKhoaTaiKhoan(nguoiDung)"
                  >
                    Mở khóa
                  </button>

                </div>
              </td>
            </tr>

            <!-- KHÔNG CÓ DỮ LIỆU -->
            <tr v-if="danhSachDangXem.length === 0">
              <td colspan="7">
                <div class="empty-state">
                  <div class="empty-icon">♙</div>

                  <h3>Không có tài khoản trong danh sách này</h3>

                  <p>
                    {{
                      tabHienTai === 'da-cap-quyen'
                        ? 'Chưa có tài khoản nào được cấp quyền quản trị.'
                        : 'Tất cả tài khoản đã được cấp quyền quản trị.'
                    }}
                  </p>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- CHÂN BẢNG -->
      <div class="table-footer">
        <span>
          Đang hiển thị {{ danhSachDangXem.length }} tài khoản
        </span>

        <span class="api-indicator">
          <span class="status-dot"></span>
          Dữ liệu từ API Laravel
        </span>
      </div>
    </section>
  </div>
</template>


<script>
import axios from 'axios';

const API = 'http://127.0.0.1:8000/api';

export default {
  name: 'QuanLyNguoiDung',

  data() {
    return {
      danhSachNguoiDung: [],

      tabHienTai: 'da-cap-quyen',

      vaiTroDuocChon: {},

      dangTai: false,
      dangXuLy: null,

      loi: '',
      // Lưu danh sách tài khoản có ảnh bị lỗi
      anhDaiDienLoi: {},
    };
  },

  computed: {
    // Danh sách đã cấp quyền quản trị
    danhSachDaCapQuyen() {
      return this.danhSachNguoiDung.filter((item) =>
        [1, 2, 3].includes(Number(item.MaQuyen))
      );
    },

    // Danh sách khách hàng chưa cấp quyền
    danhSachChuaCapQuyen() {
      return this.danhSachNguoiDung.filter(
        (item) => Number(item.MaQuyen) === 4
      );
    },

    // Danh sách đang được hiển thị
    danhSachDangXem() {
      return this.tabHienTai === 'da-cap-quyen'
        ? this.danhSachDaCapQuyen
        : this.danhSachChuaCapQuyen;
    },

    // Tổng tài khoản bị khóa
    soTaiKhoanBiKhoa() {
      return this.danhSachNguoiDung.filter(
        (item) => item.TrangThai !== 'HoatDong'
      ).length;
    },
  },

  mounted() {
    this.getData();
  },

  methods: {

    // =========================================
    // CẤU HÌNH TOKEN
    // =========================================

    getConfig() {
      const token = localStorage.getItem('admin_token');

      return {
        headers: {
          Authorization: `Bearer ${token}`,
          Accept: 'application/json',
        },
      };
    },


    // =========================================
    // XỬ LÝ NỘI DUNG LỖI
    // =========================================

    layNoiDungLoi(error, macDinh) {
      const data = error.response?.data;

      if (data?.errors) {
        return Object.values(data.errors).flat().join('\n');
      }

      return data?.message || error.message || macDinh;
    },


    // =========================================
    // LẤY DANH SÁCH NGƯỜI DÙNG
    // =========================================

    async getData() {
      this.dangTai = true;
      this.loi = '';

      try {
        const res = await axios.get(
          `${API}/nguoi-dung/get-data`,
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(
            res.data?.message ||
            'Không lấy được danh sách người dùng.'
          );
        }

        this.danhSachNguoiDung = Array.isArray(res.data.data)
          ? res.data.data
          : [];

        // Cập nhật lại vai trò đang chọn theo dữ liệu mới nhất
        this.danhSachNguoiDung.forEach((item) => {
          this.vaiTroDuocChon[item.MaND] =
            [1, 2, 3].includes(Number(item.MaQuyen))
              ? Number(item.MaQuyen)
              : 2;
        });

      } catch (error) {
        console.error(
          'Lỗi lấy danh sách người dùng:',
          error.response?.data || error
        );

        this.loi = this.layNoiDungLoi(
          error,
          'Không thể kết nối API người dùng.'
        );

      } finally {
        this.dangTai = false;
      }
    },


    // =========================================
    // TÊN VAI TRÒ
    // =========================================

    getTenQuyen(maQuyen) {
      const tenQuyen = {
        1: 'Quản lý cửa hàng',
        2: 'Nhân viên bán hàng',
        3: 'Nhân viên kho',
        4: 'Khách hàng',
      };

      return tenQuyen[Number(maQuyen)] || 'Chưa xác định';
    },


    // =========================================
    // MÀU VAI TRÒ
    // =========================================

    getRoleClass(maQuyen) {
      const classes = {
        1: 'role-manager',
        2: 'role-sales',
        3: 'role-warehouse',
        4: 'role-customer',
      };

      return classes[Number(maQuyen)] || 'role-customer';
    },


    // =========================================
    // THÔNG BÁO
    // DÙNG GIỐNG TRANG QUẢN LÝ SẢN PHẨM
    // =========================================

    hienThongBao(message) {
      alert(message);
    },


    // =========================================
    // CẤP QUYỀN / ĐỔI VAI TRÒ
    // =========================================

    async capQuyen(nguoiDung) {
      if (this.dangXuLy === nguoiDung.MaND) return;

      const maQuyen = Number(
        this.vaiTroDuocChon[nguoiDung.MaND]
      );

      if (![1, 2, 3].includes(maQuyen)) {
        this.hienThongBao('Vui lòng chọn vai trò hợp lệ.');
        return;
      }

      if (maQuyen === Number(nguoiDung.MaQuyen)) {
        this.hienThongBao('Tài khoản đang có vai trò này rồi.');
        return;
      }

      const xacNhan = window.confirm(
        `Bạn có chắc muốn cấp vai trò "${this.getTenQuyen(maQuyen)}" cho ${nguoiDung.HoTen}?`
      );

      if (!xacNhan) return;

      this.dangXuLy = nguoiDung.MaND;

      try {
        const res = await axios.put(
          `${API}/nguoi-dung/phan-quyen`,
          {
            MaND: nguoiDung.MaND,
            MaQuyen: maQuyen,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(
            res.data?.message || 'Cấp quyền thất bại.'
          );
        }

        this.hienThongBao(
          res.data.message || 'Cấp quyền thành công.'
        );

        await this.getData();

      } catch (error) {
        console.error(
          'Lỗi cấp quyền:',
          error.response?.data || error
        );

        this.hienThongBao(
          this.layNoiDungLoi(error, 'Không thể cấp quyền.')
        );

      } finally {
        this.dangXuLy = null;
      }
    },


    // =========================================
    // THU HỒI QUYỀN
    // =========================================

    async thuHoiQuyen(nguoiDung) {
      if (this.dangXuLy === nguoiDung.MaND) return;

      const xacNhan = window.confirm(
        `Thu hồi quyền quản trị của ${nguoiDung.HoTen}? Tài khoản sẽ trở về vai trò Khách hàng.`
      );

      if (!xacNhan) return;

      this.dangXuLy = nguoiDung.MaND;

      try {
        const res = await axios.put(
          `${API}/nguoi-dung/thu-hoi-quyen`,
          {
            MaND: nguoiDung.MaND,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(
            res.data?.message || 'Thu hồi quyền thất bại.'
          );
        }

        this.hienThongBao(
          res.data.message || 'Thu hồi quyền thành công.'
        );

        await this.getData();

      } catch (error) {
        console.error(
          'Lỗi thu hồi quyền:',
          error.response?.data || error
        );

        this.hienThongBao(
          this.layNoiDungLoi(error, 'Không thể thu hồi quyền.')
        );

      } finally {
        this.dangXuLy = null;
      }
    },


    // =========================================
    // KHÓA TÀI KHOẢN
    // =========================================

    async khoaTaiKhoan(nguoiDung) {
      if (this.dangXuLy === nguoiDung.MaND) return;

      const xacNhan = window.confirm(
        `Bạn có chắc muốn khóa tài khoản ${nguoiDung.HoTen}? Tài khoản sẽ không thể tiếp tục đăng nhập.`
      );

      if (!xacNhan) return;

      this.dangXuLy = nguoiDung.MaND;

      try {
        const res = await axios.put(
          `${API}/nguoi-dung/khoa`,
          {
            MaND: nguoiDung.MaND,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(
            res.data?.message || 'Khóa tài khoản thất bại.'
          );
        }

        this.hienThongBao(
          res.data.message || 'Khóa tài khoản thành công.'
        );

        await this.getData();

      } catch (error) {
        console.error(
          'Lỗi khóa tài khoản:',
          error.response?.data || error
        );

        this.hienThongBao(
          this.layNoiDungLoi(error, 'Không thể khóa tài khoản.')
        );

      } finally {
        this.dangXuLy = null;
      }
    },


    // =========================================
    // MỞ KHÓA TÀI KHOẢN
    // =========================================

    async moKhoaTaiKhoan(nguoiDung) {
      if (this.dangXuLy === nguoiDung.MaND) return;

      const xacNhan = window.confirm(
        `Bạn có chắc muốn mở khóa tài khoản ${nguoiDung.HoTen}?`
      );

      if (!xacNhan) return;

      this.dangXuLy = nguoiDung.MaND;

      try {
        const res = await axios.put(
          `${API}/nguoi-dung/mo-khoa`,
          {
            MaND: nguoiDung.MaND,
          },
          this.getConfig()
        );

        if (!res.data?.status) {
          throw new Error(
            res.data?.message || 'Mở khóa tài khoản thất bại.'
          );
        }

        this.hienThongBao(
          res.data.message || 'Mở khóa tài khoản thành công.'
        );

        await this.getData();

      } catch (error) {
        console.error(
          'Lỗi mở khóa tài khoản:',
          error.response?.data || error
        );

        this.hienThongBao(
          this.layNoiDungLoi(error, 'Không thể mở khóa tài khoản.')
        );

      } finally {
        this.dangXuLy = null;
      }
    },


    // =========================================
// LẤY ĐƯỜNG DẪN ẢNH ĐẠI DIỆN
// =========================================

getAvatarUrl(nguoiDung) {
  const avatar =
    nguoiDung?.Avatar ||
    nguoiDung?.AnhDaiDien ||
    nguoiDung?.HinhAnhDaiDien ||
    nguoiDung?.HinhAnh ||
    nguoiDung?.avatar ||
    nguoiDung?.anh_dai_dien;

  if (!avatar || typeof avatar !== 'string') {
    return '';
  }

  const value = avatar.trim().replace(/\\/g, '/');

  if (!value) return '';

  // Đường dẫn đầy đủ
  if (/^(https?:\/\/|data:image\/)/i.test(value)) {
    return value;
  }

  const baseURL = API.replace(/\/api\/?$/, '');
  const path = value.replace(/^\/+/, '');

  // Đã có thư mục storage
  if (path.toLowerCase().startsWith('storage/')) {
    return `${baseURL}/${path}`;
  }

  // Đường dẫn lưu theo Laravel public disk
  return `${baseURL}/storage/${path}`;
},
    // =========================================
    // LỖI ẢNH ĐẠI DIỆN
    // =========================================

    anhLoi(event, maND) {
      this.anhDaiDienLoi = {
        ...this.anhDaiDienLoi,
        [maND]: true,
      };
    },

  },
};
</script>


<style scoped>
/* =========================================================
   BẢNG MÀU & BIẾN DÙNG CHUNG
   ========================================================= */
.user-page {
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
  --shadow-sm: 0 2px 8px rgba(15, 23, 42, 0.06);
  --ease: cubic-bezier(0.22, 1, 0.36, 1);

  width: 100%;
  min-width: 0;
  color: var(--text);
  font-family: "Roboto", "Inter", Arial, sans-serif;
}

.user-page * {
  box-sizing: border-box;
}

/* =========================================================
   KEYFRAMES
   ========================================================= */
@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes rowIn {
  from {
    opacity: 0;
    transform: translateX(-10px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes pulseDot {
  0%,
  100% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: 0.5;
    transform: scale(1.35);
  }
}

@keyframes shine {
  from {
    transform: translateX(-120%) skewX(-20deg);
  }
  to {
    transform: translateX(260%) skewX(-20deg);
  }
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.page-heading {
  animation: fadeUp 0.5s var(--ease) both;
}

.stat-card {
  animation: fadeUp 0.55s var(--ease) both;
}
.stat-card:nth-child(1) { animation-delay: 0.05s; }
.stat-card:nth-child(2) { animation-delay: 0.12s; }
.stat-card:nth-child(3) { animation-delay: 0.19s; }
.stat-card:nth-child(4) { animation-delay: 0.26s; }

.content-card {
  animation: fadeUp 0.6s var(--ease) 0.3s both;
}

tbody tr {
  animation: rowIn 0.4s var(--ease) both;
}
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
  font-size: 29px;
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
  line-height: 1.6;
}

/* =========================================================
   NÚT
   ========================================================= */
.btn-primary,
.btn-secondary {
  position: relative;
  overflow: hidden;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 42px;
  padding: 0 18px;
  border-radius: 11px;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    background 0.25s, border-color 0.25s;
}

.btn-primary {
  border: 0;
  color: #fff;
  background: var(--grad-primary);
  background-size: 140% 140%;
  background-position: 0% 50%;
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.32);
}

.btn-primary::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 40%;
  height: 100%;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.35),
    transparent
  );
  transform: translateX(-120%) skewX(-20deg);
  pointer-events: none;
}

.btn-primary:hover:not(:disabled) {
  transform: translateY(-2px);
  background-position: 100% 50%;
  box-shadow: 0 12px 26px rgba(79, 70, 229, 0.42);
}

.btn-primary:hover:not(:disabled)::after {
  animation: shine 0.8s var(--ease);
}

.btn-primary:active:not(:disabled) {
  transform: scale(0.97);
}

.btn-primary:disabled {
  opacity: 0.75;
  cursor: wait;
}

.btn-secondary {
  border: 1.5px solid var(--line);
  color: #475569;
  background: #fff;
}

.btn-secondary:hover {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.refresh-icon {
  display: inline-block;
  font-size: 20px;
  line-height: 1;
  transition: transform 0.5s var(--ease);
}

.btn-primary:hover:not(:disabled) .refresh-icon {
  transform: rotate(180deg);
}

/* đang tải thì icon xoay liên tục */
.btn-primary:disabled .refresh-icon {
  animation: spin 0.8s linear infinite;
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
.stat-card:nth-child(2) { --tone: #f43f5e; --tone-soft: rgba(244, 63, 94, 0.1); }
.stat-card:nth-child(3) { --tone: #10b981; --tone-soft: rgba(16, 185, 129, 0.12); }
.stat-card:nth-child(4) { --tone: #f59e0b; --tone-soft: rgba(245, 158, 11, 0.13); }

.stat-card:hover {
  transform: translateY(-6px);
  border-color: transparent;
  box-shadow: 0 16px 36px rgba(15, 23, 42, 0.12);
}

.stat-card:hover::before {
  transform: scale(1.5);
}

.stat-card:hover::after {
  transform: scaleX(1);
}

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
  flex: 0 0 54px;
  width: 54px;
  height: 54px;
  border-radius: 16px;
  color: #fff;
  font-size: 24px;
  transition: transform 0.35s var(--ease);
}

.stat-card:hover .stat-icon {
  transform: rotate(-8deg) scale(1.1);
}

.icon-blue {
  background: linear-gradient(135deg, #6366f1, #4f46e5);
  box-shadow: 0 8px 16px rgba(79, 70, 229, 0.35);
}

.icon-red {
  background: linear-gradient(135deg, #fb7185, #e11d48);
  box-shadow: 0 8px 16px rgba(244, 63, 94, 0.35);
}

.icon-green {
  background: linear-gradient(135deg, #34d399, #059669);
  box-shadow: 0 8px 16px rgba(16, 185, 129, 0.35);
}

.icon-orange {
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 8px 16px rgba(245, 158, 11, 0.38);
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
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding: 24px 26px 18px;
}

.list-heading h2 {
  margin: 0;
  color: var(--navy);
  font-size: 18px;
  font-weight: 800;
}

.list-heading p {
  margin: 7px 0 0;
  color: var(--muted);
  font-size: 12.5px;
}

.result-count {
  flex-shrink: 0;
  padding: 8px 14px;
  border-radius: 999px;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 6px 14px rgba(79, 70, 229, 0.3);
  font-size: 12px;
  font-weight: 700;
}

/* =========================================================
   TABS
   ========================================================= */
.tab-bar {
  display: flex;
  gap: 8px;
  padding: 0 26px 18px;
  border-bottom: 1px solid var(--line);
}

.tab-btn {
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 11px 16px;
  border: 1.5px solid var(--line);
  border-radius: 11px;
  color: var(--muted);
  background: #fff;
  cursor: pointer;
  font-size: 12.5px;
  font-weight: 700;
  transition: all 0.28s var(--ease);
}

.tab-btn:hover:not(.active) {
  color: var(--primary);
  border-color: #c7d2fe;
  background: #eef2ff;
  transform: translateY(-2px);
}

.tab-btn.active {
  border-color: transparent;
  color: #fff;
  background: var(--grad-primary);
  box-shadow: 0 8px 18px rgba(79, 70, 229, 0.35);
}

.tab-count {
  min-width: 24px;
  padding: 3px 8px;
  border-radius: 999px;
  color: inherit;
  background: rgba(100, 116, 139, 0.14);
  text-align: center;
  font-size: 11px;
  font-weight: 800;
}

.tab-btn.active .tab-count {
  background: rgba(255, 255, 255, 0.25);
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
  min-width: 1280px; /* cũ: 1120px */
  border-collapse: separate;
  border-spacing: 0;
  text-align: left;
}

thead th {
  padding: 15px 13px;
  color: #3730a3;
  background: linear-gradient(180deg, #eef2ff, #e0e7ff);
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.4px;
  white-space: nowrap;
}

td {
  padding: 14px 13px;
  border-bottom: 1px solid #eef2f7;
  color: #475569;
  font-size: 12px;
  vertical-align: middle;
  transition: background 0.25s;
}

th:first-child,
td:first-child {
  padding-left: 24px;
}

th:last-child,
td:last-child {
  padding-right: 22px;
}

tbody tr:hover td {
  background: #f5f7ff;
}

tbody tr:hover td:first-child {
  box-shadow: inset 4px 0 0 var(--primary);
}

tbody tr:last-child td {
  border-bottom: 0;
}

.code-column {
  width: 90px;
}

.code-badge {
  display: inline-flex;
  padding: 6px 10px;
  border-radius: 8px;
  color: #4338ca;
  background: #e0e7ff;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.3px;
}

/* =========================================================
   THÔNG TIN NGƯỜI DÙNG
   ========================================================= */
.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 200px;
}

.user-avatar {
  display: flex;
  align-items: center;
  justify-content: center;
  flex: 0 0 40px;
  width: 40px;
  height: 40px;
  border: 2px solid #fff;
  border-radius: 50%;
  object-fit: cover;
  overflow: hidden;
  box-shadow: 0 0 0 2px #c7d2fe, 0 4px 10px rgba(79, 70, 229, 0.18);
  transition: transform 0.3s var(--ease), box-shadow 0.3s var(--ease);
}

tbody tr:hover .user-avatar {
  transform: scale(1.1);
  box-shadow: 0 0 0 2px var(--primary), 0 8px 16px rgba(79, 70, 229, 0.3);
}

.avatar-fallback {
  color: #fff;
  background: var(--grad-primary);
  font-size: 15px;
  font-weight: 800;
  text-transform: uppercase;
}

.user-text {
  min-width: 0;
}

.user-name {
  color: var(--navy);
  font-size: 12.5px;
  font-weight: 700;
  transition: color 0.2s;
}

tbody tr:hover .user-name {
  color: var(--primary);
}

.user-email {
  margin-top: 4px;
  color: #94a3b8;
  font-size: 11px;
  overflow-wrap: anywhere;
}

.address-cell {
  min-width: 120px;
  max-width: 180px;
  line-height: 1.5;
}

/* =========================================================
   VAI TRÒ & TRẠNG THÁI
   ========================================================= */
.role-badge,
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

.role-manager {
  color: #be123c;
  background: #ffe4e6;
}

.role-sales {
  color: #3730a3;
  background: #e0e7ff;
}

.role-warehouse {
  color: #b45309;
  background: #fef3c7;
}

.role-customer {
  color: #047857;
  background: #d1fae5;
}

.status-active {
  color: #047857;
  background: #d1fae5;
}

.status-locked {
  color: #be123c;
  background: #ffe4e6;
}

.status-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: currentColor;
}

.status-active .status-dot {
  animation: pulseDot 1.8s ease-in-out infinite;
}

/* =========================================================
   THAO TÁC
   ========================================================= */
.action-column {
  min-width: 400px;
}

.actions-cell {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: nowrap; /* cũ: wrap */
  gap: 6px;
  min-width: 390px; /* cũ: 330px */
}
.role-select {
  max-width: 150px;
  min-height: 34px;
  padding: 0 8px;
  border: 1.5px solid var(--line);
  border-radius: 9px;
  color: #334155;
  background: #f8fafc;
  font-size: 11px;
  font-weight: 600;
  cursor: pointer;
  outline: 0;
  transition: all 0.25s var(--ease);
}

.role-select:hover:not(:disabled) {
  border-color: #c7d2fe;
}

.role-select:focus {
  border-color: var(--primary);
  background: #fff;
  box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.12);
}

.role-select:disabled {
  opacity: 0.55;
  cursor: not-allowed;
}

.action-btn {
  min-height: 34px;
  padding: 0 12px;
  border: 0;
  border-radius: 9px;
  color: #fff;
  cursor: pointer;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
  transition: transform 0.25s var(--ease), box-shadow 0.25s var(--ease),
    filter 0.25s;
}

.grant-btn,
.unlock-btn {
  background: var(--grad-success);
  box-shadow: 0 5px 12px rgba(16, 185, 129, 0.3);
}

.change-btn {
  background: linear-gradient(135deg, #6366f1, #2563eb);
  box-shadow: 0 5px 12px rgba(79, 70, 229, 0.3);
}

.revoke-btn {
  background: linear-gradient(135deg, #fbbf24, #f97316);
  box-shadow: 0 5px 12px rgba(245, 158, 11, 0.32);
}

.lock-btn {
  background: var(--grad-danger);
  box-shadow: 0 5px 12px rgba(244, 63, 94, 0.3);
}

.action-btn:hover:not(:disabled) {
  transform: translateY(-3px);
  filter: brightness(1.06);
}

.grant-btn:hover:not(:disabled),
.unlock-btn:hover:not(:disabled) {
  box-shadow: 0 10px 20px rgba(16, 185, 129, 0.45);
}

.change-btn:hover:not(:disabled) {
  box-shadow: 0 10px 20px rgba(79, 70, 229, 0.45);
}

.revoke-btn:hover:not(:disabled) {
  box-shadow: 0 10px 20px rgba(245, 158, 11, 0.45);
}

.lock-btn:hover:not(:disabled) {
  box-shadow: 0 10px 20px rgba(244, 63, 94, 0.45);
}

.action-btn:active:not(:disabled) {
  transform: scale(0.95);
}

.action-btn:disabled {
  opacity: 0.45;
  cursor: not-allowed;
  box-shadow: none;
  filter: grayscale(0.4);
}

.text-center {
  text-align: center;
}

/* =========================================================
   CHÂN BẢNG
   ========================================================= */
.table-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
  padding: 15px 24px;
  border-top: 1px solid var(--line);
  background: #f8fafc;
  color: var(--muted);
  font-size: 11.5px;
}

.api-indicator {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 5px 12px;
  border-radius: 999px;
  color: #047857;
  background: #d1fae5;
  font-weight: 700;
}

.api-indicator .status-dot {
  background: #10b981;
  animation: pulseDot 1.8s ease-in-out infinite;
}

/* =========================================================
   TẢI DỮ LIỆU & LỖI
   ========================================================= */
.message-box {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  min-height: 150px;
  padding: 24px;
  color: var(--muted);
  font-size: 13px;
  font-weight: 600;
}

.error-message {
  justify-content: space-between;
  color: #be123c;
  background: linear-gradient(180deg, #fff1f2, #fff);
}

.error-message p {
  margin: 5px 0 0;
  font-size: 12px;
  font-weight: 400;
}

.loading-spinner {
  width: 22px;
  height: 22px;
  border: 3px solid #c7d2fe;
  border-top-color: var(--primary);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

/* =========================================================
   DANH SÁCH TRỐNG
   ========================================================= */
.empty-state {
  padding: 46px 20px;
  text-align: center;
}

.empty-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 72px;
  height: 72px;
  margin-bottom: 12px;
  border-radius: 50%;
  color: var(--primary);
  background: #e0e7ff;
  font-size: 32px;
}

.empty-state h3 {
  margin: 0;
  color: var(--navy);
  font-size: 15px;
  font-weight: 800;
}

.empty-state p {
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 12px;
}

/* Thanh cuộn gọn */
.table-wrapper::-webkit-scrollbar {
  height: 8px;
}

.table-wrapper::-webkit-scrollbar-thumb {
  border-radius: 8px;
  background: #c7d2fe;
}

.table-wrapper::-webkit-scrollbar-thumb:hover {
  background: #818cf8;
}

/* =========================================================
   RESPONSIVE
   ========================================================= */
@media (max-width: 1100px) {
  .stats-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 650px) {
  .page-heading {
    align-items: flex-start;
    flex-direction: column;
  }

  .page-heading h1 {
    font-size: 24px;
  }

  .stats-grid {
    grid-template-columns: 1fr;
    gap: 10px;
  }

  .list-heading {
    align-items: flex-start;
    padding: 18px 16px;
  }

  .tab-bar {
    flex-wrap: wrap;
    padding: 0 16px 15px;
  }

  .tab-btn {
    flex: 1 1 100%;
    justify-content: space-between;
  }

  .table-footer {
    padding: 13px 16px;
  }
}

/* Tôn trọng người dùng tắt hiệu ứng chuyển động */
@media (prefers-reduced-motion: reduce) {
  .user-page *,
  .user-page *::before,
  .user-page *::after {
    animation: none !important;
    transition: none !important;
  }
}
</style>