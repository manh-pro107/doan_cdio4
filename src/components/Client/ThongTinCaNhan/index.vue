<template>
  <main class="profile-page">
    <section class="profile-container">

      <header class="profile-heading">
        <div>
          <span>TECHSHOP ACCOUNT</span>
          <h1>Quản lý thông tin cá nhân</h1>
          <p>Quản lý hồ sơ, thông tin liên hệ và bảo mật tài khoản.</p>
        </div>

        <button type="button" @click="$router.push('/')">
          Về trang chủ
        </button>
      </header>

      <p v-if="message" class="profile-message success">{{ message }}</p>
      <p v-if="error" class="profile-message error">{{ error }}</p>

      <section v-if="loading" class="profile-card">
        Đang tải thông tin tài khoản...
      </section>

      <template v-else>
        <!-- THÔNG TIN CÁ NHÂN -->
        <section class="profile-card">
          <h2>Thông tin cá nhân</h2>

          <div class="avatar-section">
            <div class="avatar-preview">
              <img
                v-if="avatarSrc"
                :src="avatarSrc"
                alt="Ảnh đại diện"
              />
              <i v-else class="fa-solid fa-user"></i>
            </div>

            <div>
              <strong>Ảnh đại diện</strong>
              <p>JPG, PNG, WEBP · Tối đa 5MB</p>

              <input
                ref="avatarInput"
                type="file"
                accept="image/jpeg,image/png,image/webp"
                hidden
                @change="chonAvatar"
              />

              <button
                type="button"
                class="blue-button"
                @click="$refs.avatarInput.click()"
              >
                Chọn ảnh
              </button>

              <button
                v-if="avatarFile"
                type="button"
                class="save-button"
                :disabled="avatarLoading"
                @click="capNhatAvatar"
              >
                {{ avatarLoading ? "Đang tải..." : "Lưu ảnh đại diện" }}
              </button>
            </div>
          </div>

          <form @submit.prevent="capNhatThongTin">
            <div class="profile-form-grid">
              <label>
                Họ và tên *
                <input v-model.trim="profile.HoTen" required maxlength="100" />
              </label>

              <label>
                Email
                <input :value="profile.Email" disabled />
              </label>

              <label>
                Số điện thoại
                <input
                  v-model.trim="profile.SoDienThoai"
                  maxlength="20"
                  placeholder="Nhập số điện thoại"
                />
              </label>

              <label class="full-width">
                Địa chỉ
                <textarea
                  v-model.trim="profile.DiaChi"
                  rows="3"
                  placeholder="Nhập địa chỉ của bạn"
                ></textarea>
              </label>
            </div>

            <div class="profile-actions">
              <button
                type="submit"
                class="save-button"
                :disabled="savingProfile"
              >
                {{ savingProfile ? "Đang lưu..." : "Lưu thông tin" }}
              </button>
            </div>
          </form>
        </section>

        <!-- ĐỔI MẬT KHẨU -->
        <section class="profile-card">
          <h2>Bảo mật tài khoản</h2>
          <p class="profile-description">
            Nhập mật khẩu hiện tại và mật khẩu mới để thay đổi mật khẩu.
          </p>

          <form @submit.prevent="doiMatKhau">
            <div class="profile-form-grid">
              <label>
                Mật khẩu hiện tại *
                <input
                  v-model="passwordForm.MatKhauCu"
                  type="password"
                  required
                  autocomplete="current-password"
                />
              </label>

              <label>
                Mật khẩu mới *
                <input
                  v-model="passwordForm.MatKhauMoi"
                  type="password"
                  required
                  minlength="8"
                  autocomplete="new-password"
                />
              </label>

              <label>
                Xác nhận mật khẩu mới *
                <input
                  v-model="passwordForm.MatKhauMoi_confirmation"
                  type="password"
                  required
                  minlength="8"
                  autocomplete="new-password"
                />
              </label>
            </div>

            <div class="profile-actions">
              <button
                type="submit"
                class="save-button"
                :disabled="changingPassword"
              >
                {{ changingPassword ? "Đang đổi..." : "Đổi mật khẩu" }}
              </button>
            </div>
          </form>
        </section>
      </template>

    </section>
  </main>
</template>

<script>
import axios from "axios";

const API_ROOT = "http://127.0.0.1:8000";
const API = `${API_ROOT}/api`;

export default {
  name: "ThongTinCaNhan",

  data() {
    return {
      loading: true,
      savingProfile: false,
      avatarLoading: false,
      changingPassword: false,

      message: "",
      error: "",

      profile: {
        MaND: null,
        HoTen: "",
        Email: "",
        SoDienThoai: "",
        DiaChi: "",
        Avatar: "",
        AvatarUrl: "",
      },

      avatarFile: null,
      avatarPreview: "",

      passwordForm: {
        MatKhauCu: "",
        MatKhauMoi: "",
        MatKhauMoi_confirmation: "",
      },
    };
  },

  computed: {
    avatarSrc() {
      if (this.avatarPreview) return this.avatarPreview;

      if (this.profile.AvatarUrl) {
        return this.profile.AvatarUrl;
      }

      if (!this.profile.Avatar) return "";

      if (/^https?:\/\//i.test(this.profile.Avatar)) {
        return this.profile.Avatar;
      }

      const path = this.profile.Avatar.replace(/^\/?storage\//, "");

      return `${API_ROOT}/storage/${path}`;
    },
  },

  mounted() {
    this.loadProfile();
  },

  methods: {
    config() {
      const token = localStorage.getItem("token");

      return {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      };
    },

    capNhatLocalStorage() {
      try {
        const oldUser = JSON.parse(
          localStorage.getItem("user") || "{}"
        );

        localStorage.setItem(
          "user",
          JSON.stringify({
            ...oldUser,
            ...this.profile,
          })
        );

        window.dispatchEvent(
          new Event("techshop:account-updated")
        );
      } catch (error) {
        console.error("Không cập nhật được dữ liệu tài khoản:", error);
      }
    },

    async loadProfile() {
      const token = localStorage.getItem("token");

      if (!token) {
        this.$router.replace("/dang-nhap");
        return;
      }

      this.loading = true;
      this.error = "";

      try {
        const response = await axios.get(
          `${API}/client/thong-tin-ca-nhan`,
          this.config()
        );

        if (response.data?.status !== true) {
          throw new Error(response.data?.message || "Không tải được hồ sơ.");
        }

        this.profile = {
          ...this.profile,
          ...response.data.data,
        };

        this.capNhatLocalStorage();

      } catch (error) {
        this.error =
          error.response?.data?.message ||
          error.message ||
          "Không tải được thông tin cá nhân.";
      } finally {
        this.loading = false;
      }
    },

    async capNhatThongTin() {
      this.savingProfile = true;
      this.message = "";
      this.error = "";

      try {
        const response = await axios.put(
          `${API}/client/cap-nhat-thong-tin`,
          {
            HoTen: this.profile.HoTen,
            SoDienThoai: this.profile.SoDienThoai || null,
            DiaChi: this.profile.DiaChi || null,
          },
          this.config()
        );

        if (response.data?.status !== true) {
          throw new Error(response.data?.message || "Cập nhật thất bại.");
        }

        this.profile = {
          ...this.profile,
          ...response.data.data,
        };

        this.capNhatLocalStorage();
        this.message = "Cập nhật thông tin cá nhân thành công.";

      } catch (error) {
        const errors = error.response?.data?.errors;
        this.error = errors
          ? Object.values(errors).flat().join(" ")
          : error.response?.data?.message ||
            error.message ||
            "Không thể cập nhật thông tin.";
      } finally {
        this.savingProfile = false;
      }
    },

    chonAvatar(event) {
      const file = event.target.files?.[0];

      if (!file) return;

      const allowed = [
        "image/jpeg",
        "image/png",
        "image/webp",
      ];

      if (!allowed.includes(file.type)) {
        this.error = "Chỉ chấp nhận ảnh JPG, PNG hoặc WEBP.";
        event.target.value = "";
        return;
      }

      if (file.size > 5 * 1024 * 1024) {
        this.error = "Ảnh không được vượt quá 5MB.";
        event.target.value = "";
        return;
      }

      this.error = "";
      this.message = "";

      if (this.avatarPreview) {
        URL.revokeObjectURL(this.avatarPreview);
      }

      this.avatarFile = file;
      this.avatarPreview = URL.createObjectURL(file);
    },

    async capNhatAvatar() {
      if (!this.avatarFile) return;

      this.avatarLoading = true;
      this.message = "";
      this.error = "";

      try {
        const formData = new FormData();
        formData.append("avatar", this.avatarFile);

        const response = await axios.post(
          `${API}/client/cap-nhat-avatar`,
          formData,
          this.config()
        );

        if (response.data?.status !== true) {
          throw new Error(response.data?.message || "Không tải được ảnh.");
        }

        this.profile = {
          ...this.profile,
          ...response.data.data,
        };

        this.avatarFile = null;

        if (this.avatarPreview) {
          URL.revokeObjectURL(this.avatarPreview);
          this.avatarPreview = "";
        }

        this.capNhatLocalStorage();
        this.message = "Đã cập nhật ảnh đại diện.";

      } catch (error) {
        this.error =
          error.response?.data?.message ||
          error.message ||
          "Không thể cập nhật ảnh đại diện.";
      } finally {
        this.avatarLoading = false;
      }
    },

    async doiMatKhau() {
      this.message = "";
      this.error = "";

      if (
        this.passwordForm.MatKhauMoi !==
        this.passwordForm.MatKhauMoi_confirmation
      ) {
        this.error = "Mật khẩu mới và xác nhận mật khẩu không khớp.";
        return;
      }

      this.changingPassword = true;

      try {
        const response = await axios.put(
          `${API}/client/doi-mat-khau`,
          this.passwordForm,
          this.config()
        );

        if (response.data?.status !== true) {
          throw new Error(response.data?.message || "Đổi mật khẩu thất bại.");
        }

        this.passwordForm = {
          MatKhauCu: "",
          MatKhauMoi: "",
          MatKhauMoi_confirmation: "",
        };

        this.message = "Đổi mật khẩu thành công.";

      } catch (error) {
        const errors = error.response?.data?.errors;

        this.error = errors
          ? Object.values(errors).flat().join(" ")
          : error.response?.data?.message ||
            error.message ||
            "Không thể đổi mật khẩu.";
      } finally {
        this.changingPassword = false;
      }
    },
  },
};
</script>

<style scoped>

.profile-page {
  --primary: #3867ff;
  --primary-dark: #2449d8;
  --violet: #7547f5;
  --ink: #14213d;
  --muted: #71809a;
  --line: #e5eaf4;

  min-height: 100vh;
  padding: 55px 22px 75px;
  color: var(--ink);
  background:
    radial-gradient(ellipse at 8% 5%, rgba(56, 103, 255, .13), transparent 30%),
    radial-gradient(ellipse at 95% 30%, rgba(117, 71, 245, .10), transparent 28%),
    #f3f6fc;
}

.profile-container {
  width: min(1000px, 100%);
  margin: 0 auto;
}

/* HERO HEADER */
.profile-heading {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
  min-height: 205px;
  margin-bottom: 27px;
  padding: 35px 38px;
  overflow: hidden;
  border: 1px solid rgba(255,255,255,.16);
  border-radius: 25px;
  color: white;
  background: linear-gradient(120deg, #142c63 0%, #2455cc 55%, #7047df 100%);
  box-shadow: 0 20px 50px rgba(35, 75, 175, .22);
}

.profile-heading::before {
  content: "";
  position: absolute;
  width: 280px;
  height: 280px;
  right: 70px;
  top: -165px;
  border: 1px solid rgba(255,255,255,.16);
  border-radius: 50%;
  box-shadow:
    0 0 0 25px rgba(255,255,255,.035),
    0 0 0 55px rgba(255,255,255,.025);
}

.profile-heading::after {
  content: "";
  position: absolute;
  width: 190px;
  height: 190px;
  right: -45px;
  bottom: -120px;
  border-radius: 50%;
  background: rgba(255,255,255,.10);
}

.profile-heading > div,
.profile-heading button {
  position: relative;
  z-index: 1;
}

.profile-heading span {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 7px 12px;
  border: 1px solid rgba(255,255,255,.23);
  border-radius: 30px;
  color: #dce7ff;
  background: rgba(255,255,255,.10);
  font-size: 10px;
  font-weight: 900;
  letter-spacing: 2px;
}

.profile-heading h1 {
  margin: 17px 0 9px;
  color: white;
  font-size: clamp(25px, 4vw, 36px);
  font-weight: 950;
  letter-spacing: -1.1px;
}

.profile-heading p {
  margin: 0;
  color: #d6e2ff;
  font-size: 13px;
  line-height: 1.7;
}

.profile-heading button {
  flex-shrink: 0;
  min-height: 43px;
  padding: 0 19px;
  border: 1px solid rgba(255,255,255,.35);
  border-radius: 12px;
  color: white;
  background: rgba(255,255,255,.13);
  font-size: 12px;
  font-weight: 800;
  cursor: pointer;
  backdrop-filter: blur(10px);
  transition: .2s ease;
}

.profile-heading button:hover {
  color: #1b3d91;
  background: white;
  transform: translateY(-2px);
}

/* NOTIFICATION */
.profile-message {
  display: flex;
  align-items: center;
  gap: 10px;
  margin: 0 0 18px;
  padding: 14px 17px;
  border: 1px solid;
  border-radius: 13px;
  font-size: 13px;
  font-weight: 700;
}

.profile-message.success {
  color: #087b55;
  border-color: #b9efdc;
  background: #e8fff5;
}

.profile-message.error {
  color: #c12d45;
  border-color: #ffd0d8;
  background: #fff0f3;
}

/* CONTENT CARDS */
.profile-card {
  position: relative;
  margin-bottom: 23px;
  padding: 29px 31px;
  overflow: hidden;
  border: 1px solid #e8edf6;
  border-radius: 21px;
  background: rgba(255,255,255,.96);
  box-shadow: 0 12px 35px rgba(29, 52, 100, .055);
  transition: .25s ease;
}

.profile-card:hover {
  border-color: #d8e2fa;
  box-shadow: 0 17px 42px rgba(29, 52, 100, .085);
}

.profile-card h2 {
  position: relative;
  display: flex;
  align-items: center;
  gap: 12px;
  margin: 0 0 25px;
  color: #17294b;
  font-size: 19px;
  font-weight: 950;
}

.profile-card h2::before {
  content: "";
  width: 5px;
  height: 25px;
  border-radius: 8px;
  background: linear-gradient(180deg, #3977ff, #8054ef);
}

.profile-description {
  margin: -13px 0 23px;
  color: #71809a;
  font-size: 12px;
  line-height: 1.7;
}

/* AVATAR */
.avatar-section {
  display: flex;
  align-items: center;
  gap: 22px;
  margin-bottom: 27px;
  padding: 20px;
  border: 1px solid #e7edfa;
  border-radius: 17px;
  background: linear-gradient(110deg, #f2f6ff, #f8f5ff);
}

.avatar-preview {
  position: relative;
  display: grid;
  flex-shrink: 0;
  width: 112px;
  height: 112px;
  place-items: center;
  overflow: hidden;
  border: 5px solid white;
  border-radius: 50%;
  color: white;
  background: linear-gradient(135deg, #3977ff, #8952ed);
  font-size: 38px;
  box-shadow:
    0 0 0 2px #b9ccff,
    0 9px 25px rgba(56,103,255,.22);
}

.avatar-preview img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-section > div:last-child {
  min-width: 0;
}

.avatar-section strong {
  display: block;
  color: #20345a;
  font-size: 15px;
  font-weight: 900;
}

.avatar-section p {
  margin: 6px 0 13px;
  color: #7888a3;
  font-size: 11px;
}

.blue-button,
.save-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 42px;
  padding: 0 18px;
  border: 0;
  border-radius: 11px;
  font-size: 12px;
  font-weight: 850;
  cursor: pointer;
  transition: .2s ease;
}

.blue-button {
  margin-right: 7px;
  color: #315eea;
  background: #e4ecff;
}

.blue-button:hover {
  color: white;
  background: #315eea;
  transform: translateY(-2px);
}

.save-button {
  color: white;
  background: linear-gradient(110deg, #3268f5, #654de9);
  box-shadow: 0 7px 17px rgba(56,103,255,.20);
}

.save-button:hover:not(:disabled) {
  box-shadow: 0 10px 24px rgba(56,103,255,.30);
  transform: translateY(-2px);
}

.save-button:disabled {
  opacity: .55;
  cursor: not-allowed;
  transform: none;
}

/* FORM */
.profile-form-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 21px;
}

.profile-form-grid label {
  display: flex;
  flex-direction: column;
  gap: 9px;
  color: #314362;
  font-size: 12px;
  font-weight: 850;
}

.profile-form-grid input,
.profile-form-grid textarea {
  width: 100%;
  min-height: 48px;
  padding: 12px 15px;
  border: 1px solid #dfe6f1;
  border-radius: 11px;
  outline: none;
  color: #1c2c49;
  background: #f9fbff;
  font-family: inherit;
  font-size: 13px;
  transition: .2s ease;
}

.profile-form-grid textarea {
  min-height: 100px;
  resize: vertical;
}

.profile-form-grid input::placeholder,
.profile-form-grid textarea::placeholder {
  color: #a1acc0;
}

.profile-form-grid input:hover,
.profile-form-grid textarea:hover {
  border-color: #bacbfa;
}

.profile-form-grid input:focus,
.profile-form-grid textarea:focus {
  border-color: #527bff;
  background: white;
  box-shadow: 0 0 0 4px rgba(56,103,255,.10);
}

.profile-form-grid input:disabled {
  color: #7d8aa1;
  border-style: dashed;
  background: #f0f3f8;
  cursor: not-allowed;
}

.full-width {
  grid-column: 1 / -1;
}

/* ACTIONS */
.profile-actions {
  display: flex;
  justify-content: flex-end;
  margin-top: 24px;
  padding-top: 19px;
  border-top: 1px solid #edf0f6;
}

.profile-actions .save-button {
  min-width: 155px;
  min-height: 45px;
}

/* LOADING */
.profile-card:only-child {
  color: #64748b;
  font-size: 14px;
}

/* RESPONSIVE */
@media (max-width: 700px) {
  .profile-page {
    padding: 25px 13px 45px;
  }

  .profile-heading {
    align-items: flex-start;
    flex-direction: column;
    gap: 18px;
    min-height: auto;
    padding: 27px 23px;
    border-radius: 19px;
  }

  .profile-heading h1 {
    font-size: 26px;
  }

  .profile-heading button {
    width: 100%;
  }

  .profile-card {
    padding: 22px 18px;
    border-radius: 17px;
  }

  .profile-form-grid {
    grid-template-columns: 1fr;
    gap: 17px;
  }

  .full-width {
    grid-column: auto;
  }

  .avatar-section {
    align-items: center;
    flex-direction: column;
    text-align: center;
  }

  .avatar-section > div:last-child {
    width: 100%;
  }

  .profile-actions .save-button {
    width: 100%;
  }
}

@media (max-width: 390px) {
  .avatar-preview {
    width: 95px;
    height: 95px;
  }

  .blue-button,
  .avatar-section .save-button {
    width: 100%;
    margin: 5px 0 0;
  }
}

</style>