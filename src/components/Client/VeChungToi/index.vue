<template>
  <div class="museum-page">
    <div ref="viewport" class="scene-viewport"></div>
    <div class="scene-vignette"></div>
    <div class="scene-grain"></div>

    <!-- HEADER -->
    <header class="museum-header">
      <button class="brand" type="button" @click="goHome" aria-label="Về trang chủ TECHSHOP">
        <span class="brand-symbol"><i class="fa-solid fa-bolt"></i></span>
        <span class="brand-name">TECH<span>SHOP</span></span>
        <small>DIGITAL MUSEUM</small>
      </button>

      <div class="header-center">
        <span class="live-dot"></span>
        <span>THE IMMERSIVE TECH EXPERIENCE</span>
        <b>·</b>
        <span>EST. 2026</span>
      </div>

      <div class="header-actions">
        <button class="header-location" type="button" @click="storeInfoOpen = !storeInfoOpen">
          <i class="fa-solid fa-location-dot"></i>
          <span>ĐỊA CHỈ SHOWROOM</span>
        </button>
        <button class="header-store" type="button" @click="goProducts">
          VÀO CỬA HÀNG <i class="fa-solid fa-arrow-up-right-from-square"></i>
        </button>
      </div>
    </header>

    <!-- STORE INFORMATION POPUP -->
    <aside v-if="storeInfoOpen" class="store-popover">
      <div class="store-popover-head">
        <span class="store-popover-icon"><i class="fa-solid fa-store"></i></span>
        <div>
          <small>TECHSHOP SHOWROOM</small>
          <strong>Điểm hẹn công nghệ</strong>
        </div>
        <button type="button" aria-label="Đóng thông tin cửa hàng" @click="storeInfoOpen = false">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>
      <div class="store-popover-row">
        <i class="fa-solid fa-map-pin"></i>
        <div><small>ĐỊA CHỈ</small><strong>137 Nguyễn Văn Linh, Đà Nẵng</strong></div>
      </div>
      <div class="store-popover-row">
        <i class="fa-regular fa-clock"></i>
        <div><small>GIỜ HOẠT ĐỘNG MINH HỌA</small><strong>08:00 – 21:30 · Thứ 2 – Chủ nhật</strong></div>
      </div>
      <div class="store-popover-row">
        <i class="fa-solid fa-headset"></i>
        <div><small>HỖ TRỢ</small><strong>Thông tin liên hệ hiển thị tại cửa hàng</strong></div>
      </div>
      <a class="store-map-link" href="https://www.google.com/maps/search/?api=1&query=137+Nguyen+Van+Linh%2C+Da+Nang%2C+Vietnam" target="_blank" rel="noopener noreferrer">
        MỞ GOOGLE MAPS <i class="fa-solid fa-arrow-up-right-from-square"></i>
      </a>
      <p class="store-demo-note">Thông tin địa điểm và giờ hoạt động đang được dựng làm dữ liệu minh họa cho đồ án.</p>
    </aside>

    <!-- INTRO SCREEN -->
    <section v-if="!started" class="intro-screen">
      <div class="intro-backdrop"></div>
      <div class="intro-orbit orbit-a"></div>
      <div class="intro-orbit orbit-b"></div>
      <div class="intro-orbit orbit-c"></div>
      <div class="intro-content">
        <div class="intro-kicker"><span class="live-dot"></span> A JOURNEY THROUGH TECHNOLOGY</div>
        <p class="intro-overline">WELCOME TO THE</p>
        <h1>TECH<span>SHOP</span><br /><em>DIGITAL MUSEUM</em></h1>
        <p class="intro-description">
          Khám phá không gian bảo tàng công nghệ đỉnh cao cùng chú chó Robot vàng đồng hành thông minh.
          Trải nghiệm góc nhìn 3D chân thực với bong bóng hội thoại 3D trực tiếp ngay bên cạnh chú chó.
        </p>
        <div class="intro-stats">
          <div><strong>07</strong><span>KHU TRƯNG BÀY</span></div>
          <div><strong>ROBO-K9</strong><span>CHÓ VÀNG 3D</span></div>
          <div><strong>3D</strong><span>IMMERSIVE MODE</span></div>
        </div>
        <button class="enter-button" type="button" @click="enterMuseum">
          <span class="enter-icon"><i class="fa-solid fa-play"></i></span>
          <span><strong>BẮT ĐẦU THAM QUAN</strong><small>ENTER THE EXPERIENCE</small></span>
          <i class="fa-solid fa-arrow-right enter-arrow"></i>
        </button>
        <div class="intro-foot">
          <span><b>WASD</b> DI CHUYỂN</span><i></i>
          <span><b>SPACE</b> NHẢY</span><i></i>
          <span><b>MOUSE</b> QUAN SÁT</span><i></i>
          <span><b>E</b> TƯƠNG TÁC</span>
        </div>
      </div>
      <div class="intro-photo-stack">
        <div class="intro-photo intro-photo-large">
          <img src="https://images.unsplash.com/photo-1593640408182-31c70c8268f5?auto=format&fit=crop&w=1100&q=85" alt="Không gian máy tính hiện đại" />
          <span>01 / THE NEXT GENERATION</span>
        </div>
        <div class="intro-photo intro-photo-small">
          <img src="https://images.unsplash.com/photo-1587202372775-e229f172b9d7?auto=format&fit=crop&w=700&q=85" alt="Bộ máy tính gaming" />
          <span>02 / PERFORMANCE</span>
        </div>
        <div class="intro-photo-caption">A SPACE TO EXPLORE<br /><b>TECHNOLOGY & IDEAS</b></div>
      </div>
      <div class="intro-side-note"><span>FIG. 001 — YELLOW ROBO-K9 READY</span><span>SCROLL LESS. EXPLORE MORE.</span></div>
      <div class="intro-index">01<span> / 07</span></div>
    </section>

    <!-- IN-EXPERIENCE HUD -->
    <template v-if="started">
      <div class="top-readout">
        <span>TECHSHOP / ARCHIVE 001</span>
        <span class="readout-divider"></span>
        <span>{{ currentZone }}</span>
      </div>

      <div class="visitor-hud">
        <div class="visitor-card">
          <span class="hud-caption">VISITOR JOURNAL</span>
          <strong>EXPLORER_{{ String(visitedExhibits.length).padStart(2, '0') }}</strong>
          <div class="visit-progress"><span :style="{ width: (visitedExhibits.length / exhibits.length * 100) + '%' }"></span></div>
          <small>{{ visitedExhibits.length }} / {{ exhibits.length }} KHU ĐÃ KHÁM PHÁ</small>
        </div>
        <div class="control-help">
          <span><kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd> DI CHUYỂN</span>
          <span><kbd>SPACE</kbd> NHẢY</span>
          <span><kbd>CHUỘT</kbd> QUAN SÁT</span>
          <span><kbd>E</kbd> XEM HIỆN VẬT</span>
        </div>
      </div>

      <div v-if="!activeExhibit" class="crosshair" :class="{ focused: hoveredExhibit }">
        <span></span><i></i>
      </div>

      <div v-if="hoveredExhibit && !activeExhibit" class="interact-prompt">
        <span class="prompt-key">E</span>
        <span>NHẤN ĐỂ KHÁM PHÁ</span>
        <b>{{ exhibitById(hoveredExhibit).title }}</b>
      </div>

      <nav class="exhibit-navigation" aria-label="Các khu trưng bày">
        <span class="nav-label">JUMP TO GALLERY</span>
        <button
          v-for="(item, index) in exhibits"
          :key="item.id"
          type="button"
          :class="{ visited: visitedExhibits.includes(item.id), selected: activeExhibit === item.id }"
          @click="openExhibit(item.id)"
          :aria-label="'Mở khu ' + item.title"
          :title="item.title"
        >
          <span>{{ String(index + 1).padStart(2, '0') }}</span>
          <i :class="item.icon"></i>
        </button>
      </nav>

      <div class="bottom-hud">
        <div class="bottom-current">
          <span class="bottom-kicker">NOW EXPLORING</span>
          <strong>{{ hoveredExhibit ? exhibitById(hoveredExhibit).title : currentZone }}</strong>
        </div>
        <div class="bottom-center"><span class="sound-wave"><i></i><i></i><i></i><i></i><i></i></span> IMMERSIVE MODE <b>·</b> 3D INTERACTIVE</div>
        <button type="button" @click="goProducts">KHÁM PHÁ SẢN PHẨM <i class="fa-solid fa-arrow-right"></i></button>
      </div>
    </template>

    <!-- EXHIBIT DETAIL PANEL -->
    <aside v-if="activeExhibit" class="artifact-overlay" @click.self="closeExhibit">
      <article class="artifact-panel">
        <div class="panel-topline">
          <span><i class="fa-solid fa-cube"></i> DIGITAL EXHIBITION / {{ activeExhibitData.code }}</span>
          <button type="button" class="close-panel" @click="closeExhibit" aria-label="Đóng"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="artifact-visual" :class="'artifact-visual-' + activeExhibitData.id">
          <img :src="activeExhibitData.image" :alt="activeExhibitData.title" />
          <div class="artifact-visual-shade"></div>
          <div class="visual-chip"><i :class="activeExhibitData.icon"></i></div>
          <span class="visual-corner">{{ activeExhibitData.code }} / TECHSHOP ARCHIVE</span>
          <span class="visual-year">{{ activeExhibitData.period }}</span>
        </div>
        <div class="artifact-copy">
          <div class="artifact-meta"><span>{{ activeExhibitData.era }}</span><span class="meta-line"></span><span>{{ activeExhibitData.type }}</span></div>
          <h2>{{ activeExhibitData.title }}</h2>
          <p class="artifact-lead">{{ activeExhibitData.lead }}</p>
          <p class="artifact-description">{{ activeExhibitData.description }}</p>

          <div class="artifact-facts">
            <div v-for="fact in activeExhibitData.facts" :key="fact.label" class="fact-item">
              <span>{{ fact.label }}</span><strong>{{ fact.value }}</strong>
            </div>
          </div>

          <div class="artifact-gallery" v-if="activeExhibitData.gallery && activeExhibitData.gallery.length">
            <div class="mini-gallery-heading"><span>VISUAL ARCHIVE</span><small>SELECTED MOMENTS</small></div>
            <div class="mini-gallery-grid">
              <figure v-for="photo in activeExhibitData.gallery" :key="photo.url">
                <img :src="photo.url" :alt="photo.caption" loading="lazy" />
                <figcaption>{{ photo.caption }}</figcaption>
              </figure>
            </div>
          </div>

          <div v-if="activeExhibitData.address" class="artifact-location">
            <div class="location-icon"><i class="fa-solid fa-location-dot"></i></div>
            <div class="location-copy">
              <span>TECHSHOP SHOWROOM · Đà Nẵng</span>
              <strong>{{ activeExhibitData.address }}</strong>
              <small>{{ activeExhibitData.hours }}</small>
            </div>
            <a :href="activeExhibitData.mapsUrl" target="_blank" rel="noopener noreferrer">CHỈ ĐƯỜNG <i class="fa-solid fa-arrow-up-right-from-square"></i></a>
          </div>

          <div class="artifact-quote"><i class="fa-solid fa-quote-left"></i><span>{{ activeExhibitData.quote }}</span></div>
          <div class="panel-bottom">
            <span><i class="fa-solid fa-circle-check"></i> ĐÃ GHI NHẬN VÀO HÀNH TRÌNH</span>
            <button type="button" @click="closeExhibit">TIẾP TỤC THAM QUAN <i class="fa-solid fa-arrow-right"></i></button>
          </div>
        </div>
      </article>
    </aside>

    <div class="museum-disclaimer">KHÔNG GIAN 3D VÀ NỘI DUNG TRƯNG BÀY MÔ PHỎNG · ĐỒ ÁN TECHSHOP</div>
  </div>
</template>

<script>
import * as THREE from "three";
import { PointerLockControls } from "three/addons/controls/PointerLockControls.js";
import { markRaw } from "vue";

const PHOTO = {
  showroom: "https://images.unsplash.com/photo-1497366754035-f200968a6e72?auto=format&fit=crop&w=1200&q=82",
  gaming: "https://images.unsplash.com/photo-1593640408182-31c70c8268f5?auto=format&fit=crop&w=1200&q=82",
  pc: "https://images.unsplash.com/photo-1587202372775-e229f172b9d7?auto=format&fit=crop&w=1200&q=82",
  laptop: "https://images.unsplash.com/photo-1496181133206-80ce9b88a853?auto=format&fit=crop&w=1200&q=82",
  keyboard: "https://images.unsplash.com/photo-1587829741301-dc798b83add3?auto=format&fit=crop&w=1200&q=82",
  components: "https://images.unsplash.com/photo-1518770660439-4636190af475?auto=format&fit=crop&w=1200&q=82",
  mouse: "https://images.unsplash.com/photo-1527864550417-7fd91fc51a46?auto=format&fit=crop&w=1200&q=82",
  team: "https://images.unsplash.com/photo-1522071820081-009f0129c71c?auto=format&fit=crop&w=1200&q=82",
  office: "https://images.unsplash.com/photo-1497366216548-37526070297c?auto=format&fit=crop&w=1200&q=82",
  coding: "https://images.unsplash.com/photo-1498050108023-c5249f4df085?auto=format&fit=crop&w=1200&q=82",
  tech: "https://images.unsplash.com/photo-1550745165-9bc0b252726f?auto=format&fit=crop&w=1200&q=82",
  monitor: "https://images.unsplash.com/photo-1593305841991-05c297ba4575?auto=format&fit=crop&w=1200&q=82",
  meeting: "https://images.unsplash.com/photo-1542744173-8e7e53415bb0?auto=format&fit=crop&w=1200&q=82",
  desk: "https://images.unsplash.com/photo-1497366811353-6870744d04b2?auto=format&fit=crop&w=1200&q=82",
  circuit: "https://images.unsplash.com/photo-1519389950473-47ba0277781c?auto=format&fit=crop&w=1200&q=82",
  server: "https://images.unsplash.com/photo-1558494949-ef010cbdcc31?auto=format&fit=crop&w=1200&q=82"
};

export default {
  name: "VeChungToi",
  data() {
    return {
      started: false,
      activeExhibit: null,
      hoveredExhibit: null,
      visitedExhibits: [],
      currentZone: "GRAND ENTRANCE HALL",
      storeInfoOpen: false,
      webglError: false,
      dogMessages: [
        "Gâu gâu! Chào bạn đến với Techshop! 🐶",
        // "Gâu! Khu vực trưng bày này đỉnh quá chủ nhân ơi!",
        // "Gâu gâu! Nhấn phím E để khám phá hiện vật này nhé!",
        // "Gâu gâu! gâu gâu gâu au huuuuuuuuuuuuuuuuuuuuu",
        "ẳng ẳng! tôi là messi grừ, grừrừ",
        "Gâu gâu! ronaldo là chủ nhân của tôi",
        "Gâu gâu! bố tôi lầ infatino chủ tịch fifa",
        // "Gâu! Đồ họa phòng triển lãm mượt như game luôn!",
        // "Gâu gâu! Ghé thăm showroom TECHSHOP tại Đà Nẵng nha!"
        
      ],
      dogMsgTimer: null,
      dogSpeechSprite: null,
      dogSpeechTexture: null,
      exhibits: [
        {
          id: "origin", code: "EX-001", period: "1970 — 1999", era: "THE BEGINNING", type: "PERSONAL COMPUTING",
          title: "Kỷ nguyên máy tính cá nhân", icon: "fa-solid fa-computer", image: PHOTO.tech,
          lead: "Khi chiếc máy tính dần rời khỏi phòng thí nghiệm để trở thành công cụ của từng gia đình, lớp học và văn phòng.",
          description: "Khu trưng bày tái hiện bước chuyển từ những bộ máy cồng kềnh, màn hình CRT đến máy tính cá nhân phổ biến hơn.",
          facts: [{ label: "BIỂU TƯỢNG", value: "CRT · Desktop" }, { label: "THÀNH PHẦN", value: "CPU · RAM · HDD" }, { label: "TÁC ĐỘNG", value: "Máy tính cá nhân" }],
          gallery: [{ url: PHOTO.tech, caption: "Những thiết bị đầu tiên" }, { url: PHOTO.components, caption: "Nền tảng phần cứng" }],
          quote: "Mỗi bước tiến lớn đều bắt đầu từ mong muốn đưa công nghệ đến gần hơn với con người."
        },
        {
          id: "power", code: "EX-002", period: "2000 — 2010", era: "THE PERFORMANCE AGE", type: "PC GAMING & PERFORMANCE",
          title: "Cuộc đua hiệu năng", icon: "fa-solid fa-gamepad", image: PHOTO.gaming,
          lead: "Hiệu năng trở thành ngôn ngữ của game thủ, nhà sáng tạo và những người thích tự tay xây dựng cấu hình.",
          description: "CPU đa nhân, card đồ họa rời, bộ nhớ tốc độ cao và hệ thống tản nhiệt khiến máy tính để bàn trở thành một nền tảng tùy biến mạnh mẽ.",
          facts: [{ label: "TRỌNG TÂM", value: "CPU · GPU" }, { label: "PHONG CÁCH", value: "Tower · RGB" }, { label: "TRẢI NGHIỆM", value: "Gaming · Sáng tạo" }],
          gallery: [{ url: PHOTO.pc, caption: "Một góc máy hiệu năng" }, { url: PHOTO.monitor, caption: "Màn hình hiển thị" }],
          quote: "Sức mạnh không nằm ở một linh kiện riêng lẻ, mà ở cách toàn bộ hệ thống phối hợp."
        },
        {
          id: "mobile", code: "EX-003", period: "2011 — 2020", era: "THE CONNECTED ERA", type: "MOBILITY & CONNECTIVITY",
          title: "Công nghệ trong chuyển động", icon: "fa-solid fa-laptop", image: PHOTO.laptop,
          lead: "Không gian làm việc rời khỏi chiếc bàn cố định. Thiết bị đồng hành cùng con người trong học tập và công việc.",
          description: "Laptop mỏng nhẹ, thiết bị không dây và màn hình di động giúp trải nghiệm công nghệ linh hoạt hơn.",
          facts: [{ label: "THIẾT BỊ", value: "Laptop · Tablet" }, { label: "KẾT NỐI", value: "Wi-Fi · Bluetooth" }, { label: "ƯU TIÊN", value: "Cơ động · Pin" }],
          gallery: [{ url: PHOTO.laptop, caption: "Thiết bị di động" }, { url: PHOTO.desk, caption: "Không gian làm việc" }],
          quote: "Thiết bị phù hợp là thiết bị theo kịp nhịp sống của người sử dụng."
        },
        {
          id: "components", code: "EX-004", period: "2015 — NOW", era: "INSIDE THE MACHINE", type: "COMPONENTS & HARDWARE",
          title: "Thế giới bên trong linh kiện", icon: "fa-solid fa-microchip", image: PHOTO.components,
          lead: "Một hệ thống hoàn chỉnh được tạo nên từ hàng chục lựa chọn nhỏ, nơi mỗi linh kiện có một vai trò riêng.",
          description: "Từ bo mạch chủ, CPU, RAM đến SSD và card đồ họa, các thành phần tương thích hoàn hảo để tối ưu hóa hiệu năng.",
          facts: [{ label: "NỀN TẢNG", value: "Mainboard" }, { label: "XỬ LÝ", value: "CPU · GPU" }, { label: "LƯU TRỮ", value: "RAM · SSD" }],
          gallery: [{ url: PHOTO.components, caption: "Bo mạch và linh kiện" }, { url: PHOTO.circuit, caption: "Mạch điện tử" }],
          quote: "Hiểu cấu tạo là bước đầu để xây dựng một cấu hình phù hợp."
        },
        {
          id: "studio", code: "EX-005", period: "2020 — NOW", era: "THE CREATOR SPACE", type: "WORK · STUDY · CREATE",
          title: "Không gian sáng tạo cá nhân", icon: "fa-solid fa-pen-ruler", image: PHOTO.coding,
          lead: "Một chiếc bàn, một màn hình và những công cụ đúng nhu cầu có thể trở thành nơi ý tưởng được biến thành sản phẩm.",
          description: "Không gian làm việc phục vụ lập trình, thiết kế, dựng phim và sáng tạo nội dung số.",
          facts: [{ label: "CÔNG VIỆC", value: "Code · Design" }, { label: "THIẾT BỊ", value: "Monitor · Gear" }, { label: "MỤC TIÊU", value: "Tập trung · Sáng tạo" }],
          gallery: [{ url: PHOTO.coding, caption: "Góc làm việc sáng tạo" }, { url: PHOTO.keyboard, caption: "Thiết bị thao tác" }],
          quote: "Một setup tốt không cần giống ai — nó cần phù hợp với cách bạn làm việc."
        },
        {
          id: "techshop", code: "EX-006", period: "TECHSHOP / 2026", era: "OUR BRAND EXPERIENCE", type: "THE TECHSHOP SHOWROOM",
          title: "TECHSHOP — Điểm hẹn công nghệ", icon: "fa-solid fa-store", image: PHOTO.showroom,
          lead: "Không gian để khách hàng quan sát, tìm hiểu và lựa chọn thiết bị công nghệ theo nhu cầu.",
          description: "Tại TECHSHOP, các nhóm sản phẩm được phân khu trực quan: laptop, PC gaming, linh kiện và phụ kiện cao cấp.",
          facts: [{ label: "ĐỊNH HƯỚNG", value: "Dễ khám phá" }, { label: "DANH MỤC", value: "PC · Laptop · Gear" }, { label: "TRẢI NGHIỆM", value: "Tư vấn · Lựa chọn" }],
          gallery: [{ url: PHOTO.showroom, caption: "Không gian showroom minh họa" }, { url: PHOTO.team, caption: "Không gian làm việc minh họa" }],
          address: "137 Nguyễn Văn Linh, Đà Nẵng",
          hours: "Giờ hoạt động minh họa: 08:00 – 21:30, tất cả các ngày",
          mapsUrl: "https://www.google.com/maps/search/?api=1&query=137+Nguyen+Van+Linh%2C+Da+Nang%2C+Vietnam",
          quote: "TECHSHOP hướng đến việc biến quá trình tìm hiểu thiết bị thành một trải nghiệm rõ ràng và thú vị."
        },
        {
          id: "future", code: "EX-007", period: "NEXT CHAPTER", era: "BEYOND THE SCREEN", type: "THE FUTURE OF TECHNOLOGY",
          title: "Chương tiếp theo của công nghệ", icon: "fa-solid fa-atom", image: PHOTO.server,
          lead: "Công nghệ tiếp tục thay đổi cách chúng ta học hỏi, làm việc, kết nối và hình dung về tương lai.",
          description: "AI, điện toán hiệu năng cao và thiết bị thông minh mở rộng giới hạn của con người.",
          facts: [{ label: "XU HƯỚNG", value: "AI · Smart Tech" }, { label: "NỀN TẢNG", value: "Compute · Cloud" }, { label: "TƯƠNG LAI", value: "Human + Tech" }],
          gallery: [{ url: PHOTO.server, caption: "Hạ tầng tính toán" }, { url: PHOTO.meeting, caption: "Ý tưởng và cộng tác" }],
          quote: "Tương lai không chỉ là thứ chúng ta chờ đợi — đó là thứ chúng ta cùng tạo ra."
        }
      ],
      wallPhotos: [
        { side: "left", z: 24, image: PHOTO.showroom, title: "WELCOME TO TECHSHOP", subtitle: "A PLACE FOR EVERY TECH IDEA", exhibit: "techshop" },
        { side: "right", z: 23, image: PHOTO.team, title: "PEOPLE & IDEAS", subtitle: "CREATIVITY STARTS TOGETHER", exhibit: "studio" },
        { side: "left", z: 11, image: PHOTO.tech, title: "THE FIRST MACHINES", subtitle: "PERSONAL COMPUTING / ARCHIVE", exhibit: "origin" },
        { side: "right", z: 9, image: PHOTO.components, title: "INSIDE THE MACHINE", subtitle: "THE HARDWARE COLLECTION", exhibit: "components" },
        { side: "left", z: -3, image: PHOTO.gaming, title: "PLAY WITHOUT LIMITS", subtitle: "GAMING CULTURE / 01", exhibit: "power" },
        { side: "right", z: -5, image: PHOTO.pc, title: "BUILD YOUR POWER", subtitle: "CUSTOM PC / PERFORMANCE", exhibit: "power" },
        { side: "left", z: -17, image: PHOTO.laptop, title: "TECH ON THE MOVE", subtitle: "MOBILE COMPUTING / 02", exhibit: "mobile" },
        { side: "right", z: -19, image: PHOTO.keyboard, title: "THE DETAILS MATTER", subtitle: "KEYBOARD · MOUSE · GEAR", exhibit: "components" },
        { side: "left", z: -31, image: PHOTO.coding, title: "CREATE YOUR SPACE", subtitle: "WORK · STUDY · CREATE", exhibit: "studio" },
        { side: "right", z: -33, image: PHOTO.office, title: "A PLACE TO THINK", subtitle: "DESIGN YOUR WORKFLOW", exhibit: "studio" },
        { side: "left", z: -45, image: PHOTO.showroom, title: "EXPLORE THE SHOWROOM", subtitle: "LAPTOP · PC · ACCESSORIES", exhibit: "techshop" },
        { side: "right", z: -47, image: PHOTO.mouse, title: "COMPLETE YOUR SETUP", subtitle: "PERIPHERALS & ACCESSORIES", exhibit: "techshop" },
        { side: "left", z: -59, image: PHOTO.server, title: "BEYOND TODAY", subtitle: "THE FUTURE LAB", exhibit: "future" },
        { side: "right", z: -61, image: PHOTO.circuit, title: "THINK IN SYSTEMS", subtitle: "TECHNOLOGY IN MOTION", exhibit: "future" }
      ],
      keys: {},
      animationFrame: null,
      resizeObserver: null,
      textureLoader: null,
      velocity: null,
      verticalVelocity: 0,
      isJumping: false,
      canJump: true,
      robotDog: null,
      dogLegs: [],
      isUnmounted: false
    };
  },
  computed: {
    activeExhibitData() {
      return this.exhibits.find(item => item.id === this.activeExhibit) || this.exhibits[0];
    }
  },
  mounted() {
    this.$nextTick(() => this.initMuseum());
    window.addEventListener("keydown", this.onKeyDown);
    window.addEventListener("keyup", this.onKeyUp);
    window.addEventListener("blur", this.clearKeys);
  },
  beforeUnmount() {
    this.isUnmounted = true;
    window.removeEventListener("keydown", this.onKeyDown);
    window.removeEventListener("keyup", this.onKeyUp);
    window.removeEventListener("blur", this.clearKeys);
    if (this.dogMsgTimer) clearInterval(this.dogMsgTimer);
    if (this.animationFrame) cancelAnimationFrame(this.animationFrame);
    if (this.resizeObserver) this.resizeObserver.disconnect();
    if (this.controls) this.controls.dispose();
    if (this.renderer) {
      this.renderer.domElement.removeEventListener("click", this.onCanvasClick);
      this.renderer.dispose();
      this.renderer.domElement.remove();
    }
    if (this.scene) {
      this.scene.traverse(object => {
        object.geometry?.dispose?.();
        if (Array.isArray(object.material)) {
          object.material.forEach(material => this.disposeMaterial(material));
        } else if (object.material) {
          this.disposeMaterial(object.material);
        }
      });
    }
  },
  methods: {
    disposeMaterial(material) {
      if (!material) return;
      if (material.map) material.map.dispose();
      if (material.emissiveMap) material.emissiveMap.dispose();
      material.dispose?.();
    },
    exhibitById(id) {
      return this.exhibits.find(item => item.id === id) || this.exhibits[0];
    },
    initMuseum() {
      const host = this.$refs.viewport;
      if (!host) return;
      try {
        this.scene = new THREE.Scene();
        this.scene.background = new THREE.Color(0x040710);
        this.scene.fog = new THREE.FogExp2(0x040710, 0.009);

        this.camera = new THREE.PerspectiveCamera(72, Math.max(host.clientWidth, 1) / Math.max(host.clientHeight, 1), 0.1, 150);
        this.camera.position.set(0, 1.68, 35);
        this.camera.lookAt(0, 1.68, 0);
        this.velocity = markRaw(new THREE.Vector3());

        this.renderer = new THREE.WebGLRenderer({ antialias: true, alpha: false, powerPreference: "high-performance" });
        this.renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 1.5));
        this.renderer.setSize(host.clientWidth, host.clientHeight);
        this.renderer.outputColorSpace = THREE.SRGBColorSpace;
        this.renderer.toneMapping = THREE.ACESFilmicToneMapping;
        this.renderer.toneMappingExposure = 1.3;
        this.renderer.shadowMap.enabled = true;
        this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        host.appendChild(this.renderer.domElement);

        this.controls = new PointerLockControls(this.camera, this.renderer.domElement);
        this.textureLoader = new THREE.TextureLoader();
        this.textureLoader.setCrossOrigin("anonymous");
        this.interactionTargets = [];
        this.raycaster = new THREE.Raycaster();
        this.centerPoint = new THREE.Vector2(0, 0);
        this.clock = new THREE.Clock();

        this.buildRoom();
        this.buildReception();
        this.buildExhibits();
        this.buildWallGallery();
        this.buildCeilingDetails();
        this.buildRobotDog();
        this.addParticles();

        this.renderer.domElement.addEventListener("click", this.onCanvasClick);
        this.resizeObserver = new ResizeObserver(() => this.onResize());
        this.resizeObserver.observe(host);
        this.animate();
      } catch (error) {
        console.error("Không thể khởi tạo không gian 3D:", error);
        this.webglError = true;
      }
    },
    material(color, roughness = 0.35, metalness = 0.4, extra = {}) {
      return new THREE.MeshStandardMaterial({ color, roughness, metalness, ...extra });
    },
    addBox(parent, width, height, depth, color, x, y, z, options = {}) {
      const mesh = new THREE.Mesh(
        new THREE.BoxGeometry(width, height, depth),
        this.material(color, options.roughness ?? 0.35, options.metalness ?? 0.4, options.material || {})
      );
      mesh.position.set(x, y, z);
      mesh.castShadow = options.castShadow !== false;
      mesh.receiveShadow = options.receiveShadow !== false;
      if (options.rotationY) mesh.rotation.y = options.rotationY;
      parent.add(mesh);
      return mesh;
    },
    addGlowBar(parent, x, y, z, width, height, depth, color, opacity = 1) {
      const bar = new THREE.Mesh(
        new THREE.BoxGeometry(width, height, depth),
        new THREE.MeshBasicMaterial({ color, transparent: opacity < 1, opacity })
      );
      bar.position.set(x, y, z);
      parent.add(bar);
      return bar;
    },
    makeTextSprite(title, subtitle, color = "#5ee1ff", scaleX = 4.5, scaleY = 1.25) {
      const canvas = document.createElement("canvas");
      canvas.width = 1024;
      canvas.height = 320;
      const ctx = canvas.getContext("2d");
      const gradient = ctx.createLinearGradient(0, 0, 1024, 320);
      gradient.addColorStop(0, "rgba(4,10,22,.98)");
      gradient.addColorStop(1, "rgba(10,22,44,.95)");
      ctx.fillStyle = gradient;
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.strokeStyle = color;
      ctx.lineWidth = 4;
      ctx.strokeRect(7, 7, 1010, 306);
      ctx.fillStyle = color;
      ctx.font = "bold 29px 'Space Grotesk', Arial";
      ctx.fillText(String(subtitle).toUpperCase(), 40, 76);
      ctx.fillStyle = "#f5f9ff";
      ctx.font = "bold 58px 'Space Grotesk', Arial";
      ctx.fillText(String(title).toUpperCase().slice(0, 27), 40, 174);
      ctx.fillStyle = "#8aa8d0";
      ctx.font = "22px Arial";
      ctx.fillText("TECHSHOP DIGITAL MUSEUM  /  EXHIBITION", 40, 255);
      const texture = new THREE.CanvasTexture(canvas);
      texture.colorSpace = THREE.SRGBColorSpace;
      const sprite = new THREE.Sprite(new THREE.SpriteMaterial({ map: texture, transparent: true, depthWrite: false }));
      sprite.scale.set(scaleX, scaleY, 1);
      return sprite;
    },
    createSpeechBubbleTexture(message) {
  const canvas = document.createElement("canvas");
  canvas.width = 512;
  canvas.height = 256;
  const ctx = canvas.getContext("2d");
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Khung bong bóng
  ctx.fillStyle = "rgba(6, 18, 36, 0.96)";
  ctx.strokeStyle = "#ffcc00";
  ctx.lineWidth = 6;
  ctx.beginPath();
  ctx.moveTo(40, 20);
  ctx.lineTo(472, 20);
  ctx.quadraticCurveTo(492, 20, 492, 40);
  ctx.lineTo(492, 180);
  ctx.quadraticCurveTo(492, 200, 472, 200);
  ctx.lineTo(276, 200);
  ctx.lineTo(256, 240);
  ctx.lineTo(236, 200);
  ctx.lineTo(40, 200);
  ctx.quadraticCurveTo(20, 200, 20, 180);
  ctx.lineTo(20, 40);
  ctx.quadraticCurveTo(20, 20, 40, 20);
  ctx.closePath();
  ctx.fill();
  ctx.stroke();

  // Chữ tự xuống dòng
  ctx.fillStyle = "#ffffff";
  ctx.font = "bold 28px 'Be Vietnam Pro', Arial, sans-serif";
  ctx.textAlign = "center";
  ctx.textBaseline = "middle";

  const maxWidth = 420;
  const words = String(message).split(" ");
  const lines = [];
  let line = "";
  words.forEach(word => {
    const test = line ? line + " " + word : word;
    if (ctx.measureText(test).width > maxWidth && line) {
      lines.push(line);
      line = word;
    } else {
      line = test;
    }
  });
  if (line) lines.push(line);

  const lineHeight = 36;
  const startY = 110 - ((lines.length - 1) * lineHeight) / 2;
  lines.slice(0, 3).forEach((l, i) => ctx.fillText(l, 256, startY + i * lineHeight));

  const texture = markRaw(new THREE.CanvasTexture(canvas));
  texture.colorSpace = THREE.SRGBColorSpace;
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;
  texture.needsUpdate = true;
  return texture;
},
updateDogSpeech(msg) {
  if (!this.dogSpeechSprite) return;
  if (this.dogSpeechTexture) this.dogSpeechTexture.dispose();
  this.dogSpeechTexture = this.createSpeechBubbleTexture(msg);
  this.dogSpeechSprite.material.map = this.dogSpeechTexture;
  this.dogSpeechSprite.material.needsUpdate = true;
},
    buildRoom() {
      const scene = this.scene;
      scene.add(new THREE.HemisphereLight(0xbce2ff, 0x080e1b, 1.9));
      const ambient = new THREE.AmbientLight(0x92baf0, 0.5);
      scene.add(ambient);

      const mainLight = new THREE.DirectionalLight(0xe8f4ff, 2.6);
      mainLight.position.set(-6, 15, 15);
      mainLight.castShadow = true;
      mainLight.shadow.mapSize.set(1024, 1024);
      scene.add(mainLight);

      const floor = new THREE.Mesh(new THREE.PlaneGeometry(44, 118), this.material(0x0a1222, 0.15, 0.8));
      floor.rotation.x = -Math.PI / 2;
      floor.position.set(0, 0, -16);
      floor.receiveShadow = true;
      scene.add(floor);

      const ceiling = new THREE.Mesh(new THREE.PlaneGeometry(44, 118), this.material(0x040812, 0.85, 0.1));
      ceiling.rotation.x = Math.PI / 2;
      ceiling.position.set(0, 8, -16);
      scene.add(ceiling);

      this.addBox(scene, 0.55, 8, 118, 0x0b1528, -22, 4, -16);
      this.addBox(scene, 0.55, 8, 118, 0x0b1528, 22, 4, -16);
      this.addBox(scene, 44, 8, 0.55, 0x0b1528, 0, 4, 43);
      this.addBox(scene, 44, 8, 0.55, 0x0b1528, 0, 4, -75);

      for (let x = -20; x <= 20; x += 4) {
        this.addGlowBar(scene, x, 0.012, -16, 0.015, 0.015, 116, 0x31629e, 0.45);
      }
      for (let z = 38; z >= -70; z -= 4) {
        this.addGlowBar(scene, 0, 0.014, z, 42, 0.015, 0.015, 0x33649e, 0.4);
      }
      for (const x of [-20.6, 20.6]) {
        this.addGlowBar(scene, x, 0.035, -16, 0.09, 0.04, 115, 0x3bf0ff, 0.98);
        this.addGlowBar(scene, x * 0.965, 0.03, -16, 0.035, 0.03, 115, 0xa48aff, 0.92);
      }

      for (let z = 36; z >= -68; z -= 13) {
        for (const x of [-21.1, 21.1]) {
          this.addBox(scene, 0.32, 7.6, 0.42, 0x142748, x, 3.8, z, { roughness: 0.25, metalness: 0.7 });
          this.addGlowBar(scene, x + (x < 0 ? 0.19 : -0.19), 4, z, 0.05, 6.2, 0.06, 0x3ba8ff, 0.9);
          this.addGlowBar(scene, x + (x < 0 ? 0.19 : -0.19), 0.5, z, 0.05, 0.05, 0.06, 0x8b8ff8, 0.95);
        }
      }

      for (let z = 31; z >= -66; z -= 13) {
        this.addBox(scene, 0.12, 5.4, 7.3, 0x112038, -21.58, 4, z, { roughness: 0.3, metalness: 0.5 });
        this.addBox(scene, 0.12, 5.4, 7.3, 0x112038, 21.58, 4, z, { roughness: 0.3, metalness: 0.5 });
      }

      for (let z = 35; z >= -68; z -= 10) {
        this.addBox(scene, 39, 0.18, 0.38, 0x142542, 0, 7.55, z, { roughness: 0.28, metalness: 0.75 });
        this.addGlowBar(scene, 0, 7.42, z, 12, 0.04, 0.09, 0x7be8ff, 0.85);
        for (const x of [-11, 0, 11]) {
          const light = new THREE.PointLight(x === 0 ? 0x9be0ff : 0xb49eff, x === 0 ? 1.9 : 1.3, 15, 2);
          light.position.set(x, 6.6, z);
          scene.add(light);
          const fixture = new THREE.Mesh(new THREE.CylinderGeometry(0.22, 0.28, 0.12, 20), this.material(0x223a5e, 0.2, 0.8));
          fixture.position.set(x, 7.35, z);
          scene.add(fixture);
          this.addGlowBar(scene, x, 7.27, z, 0.36, 0.03, 0.36, 0xe0f6ff, 0.98);
        }
      }

      this.addBox(scene, 15, 0.3, 0.35, 0x1c3458, 0, 6.5, 40.5);
      this.addBox(scene, 0.32, 6.2, 0.36, 0x1c3458, -7.3, 3.4, 40.5);
      this.addBox(scene, 0.32, 6.2, 0.36, 0x1c3458, 7.3, 3.4, 40.5);
      const entrySign = this.makeTextSprite("WELCOME TO TECHSHOP", "Your journey starts here", "#5ef3ff", 8.5, 2.15);
      entrySign.position.set(0, 5.45, 29.6);
      scene.add(entrySign);

      const farSign = this.makeTextSprite("THE FUTURE IS YOURS", "Thank you for exploring the archive", "#c7abff", 8.6, 2.1);
      farSign.position.set(0, 5.0, -73.5);
      scene.add(farSign);

      this.addGlowBar(scene, 0, 0.025, -16, 0.04, 0.02, 104, 0x5fa8ff, 0.9);
      const timeline = [
        { z: 16, year: "1980" }, { z: 2, year: "2000" }, { z: -12, year: "2010" },
        { z: -26, year: "2015" }, { z: -40, year: "2020" }, { z: -54, year: "2026" }, { z: -66, year: "NEXT" }
      ];
      timeline.forEach((point, index) => {
        this.addGlowBar(scene, 0, 0.04, point.z, 1.35, 0.03, 0.07, index === 6 ? 0xd4bcff : 0x7bf2ff, 0.98);
        const marker = this.makeTextSprite(point.year, "TIMELINE", index === 6 ? "#dfccff" : "#8be8ff", 1.5, 0.48);
        marker.position.set(0.95, 0.36, point.z);
        scene.add(marker);
      });
    },
    buildReception() {
      const group = new THREE.Group();
      group.position.set(11.8, 0, 28.2);
      group.rotation.y = -Math.PI / 2;
      this.scene.add(group);
      this.addBox(group, 5.3, 1.05, 1.35, 0x172844, 0, 0.72, 0, { roughness: 0.2, metalness: 0.7 });
      this.addBox(group, 5.0, 0.12, 1.48, 0x3a5c88, 0, 1.3, 0, { roughness: 0.12, metalness: 0.85 });
      this.addGlowBar(group, 0, 0.82, 0.69, 4.8, 0.04, 0.04, 0x5ef3ff, 0.99);
      const frontLogo = this.makeTextSprite("TECHSHOP", "Welcome desk", "#5ef3ff", 2.5, 0.78);
      frontLogo.position.set(0, 0.76, 0.73);
      group.add(frontLogo);
      this.addBox(group, 1.0, 0.12, 0.66, 0x132238, 0.6, 1.42, -0.05);
      const monitor = this.addBox(group, 1.1, 0.72, 0.08, 0x08101d, 0.6, 1.9, -0.3);
      monitor.material = this.material(0x08101d, 0.15, 0.65, { emissive: 0x1b65aa, emissiveIntensity: 0.4 });
      const monitorScreen = new THREE.Mesh(new THREE.PlaneGeometry(0.92, 0.54), new THREE.MeshBasicMaterial({ color: 0x3ba3f5 }));
      monitorScreen.position.set(0.6, 1.9, -0.245);
      group.add(monitorScreen);
      const deskGlow = new THREE.PointLight(0x4ac7ff, 1.6, 6);
      deskGlow.position.set(0, 2.3, 0.5);
      group.add(deskGlow);
      const sign = this.makeTextSprite("INFORMATION", "Need a little guidance? Start here", "#a3f0ff", 4.2, 1.2);
      sign.position.set(0, 3.15, -0.7);
      group.add(sign);
    },
    buildExhibits() {
      const placements = [
        { id: "origin", x: -7.2, z: 16, color: 0x5ee1ff, shape: "round" },
        { id: "power", x: 7.2, z: 2, color: 0xb892ff, shape: "round" },
        { id: "mobile", x: -7.2, z: -12, color: 0x3bf3d4, shape: "square" },
        { id: "components", x: 7.2, z: -26, color: 0x75beff, shape: "square" },
        { id: "studio", x: -7.2, z: -40, color: 0xff9bbd, shape: "round" },
        { id: "techshop", x: 7.2, z: -54, color: 0xffcb78, shape: "square" },
        { id: "future", x: 0, z: -66, color: 0xcaa6ff, shape: "round" }
      ];

      placements.forEach((placement) => {
        const info = this.exhibitById(placement.id);
        const group = new THREE.Group();
        group.position.set(placement.x, 0, placement.z);
        this.scene.add(group);

        if (placement.shape === "round") {
          const base = new THREE.Mesh(new THREE.CylinderGeometry(1.55, 1.72, 0.48, 48), this.material(0x11223b, 0.15, 0.85));
          base.position.y = 0.24;
          base.castShadow = true;
          base.receiveShadow = true;
          group.add(base);
          const top = new THREE.Mesh(new THREE.CylinderGeometry(1.46, 1.46, 0.08, 48), this.material(0x203858, 0.12, 0.88));
          top.position.y = 0.51;
          group.add(top);
          const ring = new THREE.Mesh(new THREE.TorusGeometry(1.52, 0.03, 8, 72), new THREE.MeshBasicMaterial({ color: placement.color }));
          ring.rotation.x = Math.PI / 2;
          ring.position.y = 0.51;
          group.add(ring);
        } else {
          this.addBox(group, 3.35, 0.48, 3.0, 0x11223b, 0, 0.24, 0, { roughness: 0.14, metalness: 0.85 });
          this.addBox(group, 3.15, 0.08, 2.8, 0x203858, 0, 0.51, 0, { roughness: 0.12, metalness: 0.88 });
          this.addGlowBar(group, 0, 0.53, 1.4, 2.8, 0.03, 0.03, placement.color, 0.99);
          this.addGlowBar(group, 0, 0.53, -1.4, 2.8, 0.03, 0.03, placement.color, 0.99);
        }

        this.makeExhibitModel(group, placement.id, placement.color);

        const label = this.makeTextSprite(info.title, info.period, `#${new THREE.Color(placement.color).getHexString()}`, 5.0, 1.35);
        label.position.set(0, 4.15, 0);
        group.add(label);

        const beacon = new THREE.Mesh(
          new THREE.IcosahedronGeometry(0.22, 1),
          new THREE.MeshBasicMaterial({ color: placement.color })
        );
        beacon.position.set(0, 2.8, 0);
        beacon.userData.exhibitId = placement.id;
        group.add(beacon);
        const halo = new THREE.Mesh(
          new THREE.TorusGeometry(0.43, 0.02, 8, 48),
          new THREE.MeshBasicMaterial({ color: placement.color, transparent: true, opacity: 0.95 })
        );
        halo.position.copy(beacon.position);
        halo.rotation.x = Math.PI / 2;
        group.add(halo);

        group.traverse(object => {
          if (object.isMesh) {
            object.userData.exhibitId = placement.id;
            this.interactionTargets.push(object);
          }
        });
      });
    },
    makeExhibitModel(group, id, color) {
      if (id === "origin") this.makeRetroComputer(group, color);
      if (id === "power") this.makeGamingRig(group, color);
      if (id === "mobile") this.makeLaptopCollection(group, color);
      if (id === "components") this.makeHardwareCollection(group, color);
      if (id === "studio") this.makeCreatorDesk(group, color);
      if (id === "techshop") this.makeMiniStore(group, color);
      if (id === "future") this.makeFutureCore(group, color);
    },
    makeRetroComputer(group, color) {
      const monitorBody = this.addBox(group, 1.42, 1.18, 0.88, 0x9fa298, 0, 1.55, -0.18, { roughness: 0.4, metalness: 0.18 });
      const bezel = this.addBox(group, 1.12, 0.86, 0.08, 0x363e48, 0, 1.62, 0.285, { roughness: 0.35 });
      const screen = new THREE.Mesh(new THREE.PlaneGeometry(0.96, 0.69), new THREE.MeshBasicMaterial({ color: 0x51e4f5 }));
      screen.position.set(0, 1.63, 0.329);
      group.add(screen);
      this.addGlowBar(group, 0, 1.63, 0.334, 0.78, 0.02, 0.008, 0xa5fffa, 0.9);
      this.addBox(group, 0.3, 0.22, 0.42, 0x7e8278, 0, 0.91, -0.1);
      const keyboard = this.addBox(group, 1.48, 0.13, 0.62, 0x868d84, 0, 0.68, 0.76);
      keyboard.rotation.x = -0.06;
      for (let row = 0; row < 3; row++) {
        for (let col = 0; col < 11; col++) {
          this.addBox(group, 0.085, 0.025, 0.09, 0x353f4b, -0.61 + col * 0.122, 0.765, 0.55 + row * 0.13);
        }
      }
      this.addBox(group, 0.44, 0.92, 0.82, 0x5f676f, 1.1, 1.04, -0.22, { roughness: 0.4, metalness: 0.4 });
      this.addGlowBar(group, 1.1, 1.18, 0.2, 0.12, 0.05, 0.025, color);
      const mouse = new THREE.Mesh(new THREE.SphereGeometry(0.13, 16, 12), this.material(0x60676e, 0.3, 0.35));
      mouse.scale.set(0.8, 0.5, 1.25);
      mouse.position.set(1.02, 0.69, 0.88);
      group.add(mouse);
    },
    makeGamingRig(group, color) {
      const caseGroup = new THREE.Group();
      caseGroup.position.set(-0.55, 0, -0.12);
      group.add(caseGroup);
      this.addBox(caseGroup, 1.12, 1.72, 0.92, 0x112038, 0, 1.48, 0, { roughness: 0.12, metalness: 0.9 });
      const glass = new THREE.Mesh(new THREE.PlaneGeometry(0.86, 1.42), new THREE.MeshBasicMaterial({ color: 0x1a4575, transparent: true, opacity: 0.7 }));
      glass.position.set(0, 1.48, 0.47);
      caseGroup.add(glass);
      for (let i = 0; i < 3; i++) {
        const fan = new THREE.Mesh(new THREE.TorusGeometry(0.18, 0.03, 8, 36), new THREE.MeshBasicMaterial({ color }));
        fan.position.set(-0.29 + i * 0.29, 1.85, 0.49);
        caseGroup.add(fan);
        const hub = new THREE.Mesh(new THREE.SphereGeometry(0.065, 12, 8), new THREE.MeshBasicMaterial({ color: 0xf2fdff }));
        hub.position.copy(fan.position);
        caseGroup.add(hub);
      }
      this.addBox(group, 1.28, 0.14, 0.78, 0x1b3254, 0.5, 0.7, 0.85);
      this.addBox(group, 0.98, 0.05, 0.32, 0x728db2, 0.5, 0.8, 0.85);
      for (let i = 0; i < 10; i++) this.addGlowBar(group, 0.08 + i * 0.09, 0.83, 0.86, 0.03, 0.015, 0.22, color, 0.99);
      const monitor = this.addBox(group, 1.38, 0.9, 0.12, 0x1a2c48, 0.85, 2.2, -0.75);
      const monitorScreen = new THREE.Mesh(new THREE.PlaneGeometry(1.18, 0.69), new THREE.MeshBasicMaterial({ color: 0x4f3bf5 }));
      monitorScreen.position.set(0.85, 2.2, -0.682);
      group.add(monitorScreen);
      this.addBox(group, 0.15, 0.48, 0.15, 0x30496e, 0.85, 1.67, -0.75);
      const headset = new THREE.Mesh(new THREE.TorusGeometry(0.29, 0.06, 10, 36, Math.PI), this.material(0x142033, 0.2, 0.8));
      headset.position.set(1.72, 1.2, 0.45);
      headset.rotation.z = Math.PI;
      group.add(headset);
      const sideLight = new THREE.PointLight(color, 2.0, 5);
      sideLight.position.set(-0.5, 2.2, 0.8);
      group.add(sideLight);
    },
    makeLaptopCollection(group, color) {
      const laptop = new THREE.Group();
      laptop.position.set(-0.1, 0, 0.05);
      group.add(laptop);
      const base = this.addBox(laptop, 1.8, 0.1, 1.18, 0x7288a2, 0, 0.95, 0.2, { roughness: 0.18, metalness: 0.9 });
      const screenFrame = this.addBox(laptop, 1.76, 1.2, 0.1, 0x1d304b, 0, 1.63, -0.37, { roughness: 0.18, metalness: 0.8 });
      screenFrame.rotation.x = -0.12;
      const screen = new THREE.Mesh(new THREE.PlaneGeometry(1.52, 0.94), new THREE.MeshBasicMaterial({ color: 0x1fbcd4 }));
      screen.position.set(0, 1.65, -0.312);
      screen.rotation.x = -0.12;
      laptop.add(screen);
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 12; col++) this.addBox(laptop, 0.085, 0.015, 0.075, 0x1d304b, -0.53 + col * 0.096, 1.012, -0.19 + row * 0.12);
      }
      const tablet = this.addBox(group, 0.75, 0.06, 0.95, 0x152238, 1.25, 0.72, 0.15);
      tablet.rotation.y = -0.24;
      const tabletScreen = new THREE.Mesh(new THREE.PlaneGeometry(0.64, 0.82), new THREE.MeshBasicMaterial({ color: 0x2262b0 }));
      tabletScreen.position.set(1.25, 0.76, 0.19);
      tabletScreen.rotation.y = -0.24;
      group.add(tabletScreen);
      const glow = new THREE.PointLight(color, 1.7, 5);
      glow.position.set(0, 1.5, 0.7);
      group.add(glow);
    },
    makeHardwareCollection(group, color) {
      const board = this.addBox(group, 2.55, 0.12, 1.95, 0x10382e, 0, 0.78, 0, { roughness: 0.38, metalness: 0.5 });
      board.rotation.y = -0.08;
      const cpu = this.addBox(group, 0.66, 0.22, 0.66, 0xd0d9e5, -0.45, 0.98, -0.15, { roughness: 0.18, metalness: 0.92 });
      const cpuTop = this.addBox(group, 0.49, 0.03, 0.49, 0x202d3f, -0.45, 1.105, -0.15, { roughness: 0.14, metalness: 0.8 });
      this.addGlowBar(group, -0.45, 1.125, -0.15, 0.28, 0.02, 0.28, color, 0.99);
      for (let i = 0; i < 4; i++) {
        this.addBox(group, 0.14, 0.86, 0.18, 0x172437, 0.42 + i * 0.22, 1.18, -0.38, { roughness: 0.22, metalness: 0.65 });
        this.addGlowBar(group, 0.42 + i * 0.22, 1.63, -0.38, 0.08, 0.03, 0.13, i % 2 ? 0xb892ff : color, 0.99);
      }
      const gpu = this.addBox(group, 1.75, 0.32, 0.55, 0x253244, 0.25, 1.45, 0.72, { roughness: 0.18, metalness: 0.9 });
      gpu.rotation.y = -0.08;
      for (let i = 0; i < 2; i++) {
        const fan = new THREE.Mesh(new THREE.TorusGeometry(0.17, 0.03, 8, 32), new THREE.MeshBasicMaterial({ color }));
        fan.position.set(-0.22 + i * 0.56, 1.46, 1.01);
        group.add(fan);
      }
      const m2 = this.addBox(group, 0.95, 0.08, 0.18, 0x728bb0, -0.58, 1.1, -0.83, { roughness: 0.18, metalness: 0.88 });
      m2.rotation.y = -0.05;
    },
    makeCreatorDesk(group, color) {
      this.addBox(group, 2.9, 0.15, 1.45, 0x362f2e, 0, 0.92, 0.25, { roughness: 0.25, metalness: 0.45 });
      for (const x of [-1.15, 1.15]) this.addBox(group, 0.13, 0.92, 0.13, 0x42546e, x, 0.45, 0.25, { roughness: 0.22, metalness: 0.85 });
      this.addBox(group, 1.8, 1.12, 0.12, 0x132032, 0, 1.75, -0.25, { roughness: 0.15, metalness: 0.75 });
      const screen = new THREE.Mesh(new THREE.PlaneGeometry(1.57, 0.87), new THREE.MeshBasicMaterial({ color: 0x1b72b8 }));
      screen.position.set(0, 1.76, -0.181);
      group.add(screen);
      for (let i = 0; i < 6; i++) this.addGlowBar(group, -0.58 + (i % 2) * 0.16, 2.04 - i * 0.105, -0.17, 0.72 + (i % 3) * 0.12, 0.02, 0.015, i % 2 ? 0x7bf2ff : 0xcaa6ff, 0.98);
      this.addBox(group, 1.55, 0.07, 0.5, 0x1b293e, 0, 1.05, 0.63);
      for (let row = 0; row < 3; row++) for (let col = 0; col < 12; col++) this.addBox(group, 0.085, 0.015, 0.07, 0x7288a8, -0.55 + col * 0.1, 1.095, 0.43 + row * 0.12);
      const mouse = new THREE.Mesh(new THREE.SphereGeometry(0.13, 14, 12), this.material(0x111e2e, 0.18, 0.65));
      mouse.scale.set(0.9, 0.55, 1.25);
      mouse.position.set(1.08, 1.02, 0.62);
      group.add(mouse);
      const lamp = new THREE.PointLight(color, 1.8, 5);
      lamp.position.set(-1.1, 2.5, 0.2);
      group.add(lamp);
    },
    makeMiniStore(group, color) {
      this.addBox(group, 3.25, 2.75, 1.42, 0x1a2b44, 0, 1.72, -0.05, { roughness: 0.25, metalness: 0.65 });
      const glass = new THREE.Mesh(new THREE.PlaneGeometry(2.75, 1.78), new THREE.MeshBasicMaterial({ color: 0x2fb0e8, transparent: true, opacity: 0.45 }));
      glass.position.set(0, 1.78, 0.68);
      group.add(glass);
      this.addBox(group, 3.35, 0.34, 0.2, 0x08101e, 0, 3.22, 0.74);
      const sign = this.makeTextSprite("TECHSHOP", "PC · LAPTOP · ACCESSORIES", "#ffd390", 3.0, 0.85);
      sign.position.set(0, 3.22, 0.86);
      group.add(sign);
      for (let i = 0; i < 3; i++) {
        this.addBox(group, 0.62, 0.52, 0.45, 0x111f32, -0.92 + i * 0.92, 1.1, 0.5, { roughness: 0.18, metalness: 0.8 });
        this.addGlowBar(group, -0.92 + i * 0.92, 1.42, 0.74, 0.36, 0.03, 0.025, color, 0.98);
      }
      this.addBox(group, 2.6, 0.5, 0.7, 0x253a58, 0, 0.68, 1.1, { roughness: 0.2, metalness: 0.75 });
      this.addGlowBar(group, 0, 0.72, 1.46, 2.4, 0.04, 0.03, 0xffcb78, 0.99);
      const storeLight = new THREE.PointLight(color, 2.0, 6);
      storeLight.position.set(0, 2.8, 1.1);
      group.add(storeLight);
    },
    makeFutureCore(group, color) {
      const core = new THREE.Mesh(
        new THREE.IcosahedronGeometry(0.85, 2),
        this.material(0x2a5598, 0.1, 0.9, { emissive: color, emissiveIntensity: 0.75, metalness: 0.9 })
      );
      core.position.y = 1.85;
      group.add(core);
      for (let i = 0; i < 4; i++) {
        const orbit = new THREE.Mesh(
          new THREE.TorusGeometry(1.25 + i * 0.16, 0.02, 8, 88),
          new THREE.MeshBasicMaterial({ color: i % 2 ? 0xb49eff : color, transparent: true, opacity: 0.95 })
        );
        orbit.position.y = 1.85;
        orbit.rotation.set(0.5 + i * 0.42, 0.3 + i * 0.8, i * 0.38);
        group.add(orbit);
      }
      const chip = this.addBox(group, 0.68, 0.18, 0.68, 0x1a3254, 0, 0.82, 0, { roughness: 0.12, metalness: 0.95 });
      chip.material = this.material(0x1a3254, 0.12, 0.95, { emissive: 0x0f2240, emissiveIntensity: 0.4 });
      for (let i = 0; i < 6; i++) {
        this.addGlowBar(group, -0.52 + i * 0.21, 0.95, 0.42, 0.04, 0.045, 0.22, color, 0.98);
        this.addGlowBar(group, -0.52 + i * 0.21, 0.95, -0.42, 0.04, 0.045, 0.22, color, 0.98);
      }
      const coreLight = new THREE.PointLight(color, 3.5, 8);
      coreLight.position.set(0, 2.0, 0);
      group.add(coreLight);
    },
    buildRobotDog() {
      const dogGroup = new THREE.Group();
      dogGroup.position.set(2, 0, 32);

      // Yellow/Gold Cyber-Dog Body
      const bodyMat = this.material(0xffaa00, 0.18, 0.88);
      const darkMat = this.material(0x1a2233, 0.25, 0.8);

      const body = new THREE.Mesh(new THREE.BoxGeometry(0.7, 0.4, 1.1), bodyMat);
      body.position.y = 0.45;
      body.castShadow = true;
      dogGroup.add(body);

      const head = new THREE.Mesh(new THREE.BoxGeometry(0.45, 0.4, 0.5), bodyMat);
      head.position.set(0, 0.7, 0.65);
      dogGroup.add(head);

      const visor = new THREE.Mesh(
        new THREE.BoxGeometry(0.38, 0.12, 0.1),
        new THREE.MeshBasicMaterial({ color: 0x5ef3ff })
      );
      visor.position.set(0, 0.74, 0.91);
      dogGroup.add(visor);

      // 4 Running Legs (stored for animation)
      this.dogLegs = [];
      const legGeo = new THREE.BoxGeometry(0.12, 0.5, 0.12);
      const legPositions = [
        [-0.3, 0.25, 0.4], [0.3, 0.25, 0.4],
        [-0.3, 0.25, -0.4], [0.3, 0.25, -0.4]
      ];
      legPositions.forEach((pos, idx) => {
        const leg = new THREE.Mesh(legGeo, darkMat);
        leg.position.set(...pos);
        dogGroup.add(leg);
        this.dogLegs.push({ mesh: leg, index: idx });
      });

      // Tail
      const tail = new THREE.Mesh(new THREE.CylinderGeometry(0.02, 0.04, 0.5, 8), darkMat);
      tail.position.set(0, 0.75, -0.6);
      tail.rotation.x = -0.5;
      dogGroup.add(tail);

      const tailTip = new THREE.Mesh(new THREE.SphereGeometry(0.06, 8, 8), new THREE.MeshBasicMaterial({ color: 0x5ef3ff }));
      tailTip.position.set(0, 0.95, -0.78);
      dogGroup.add(tailTip);

      const dogLight = new THREE.PointLight(0xffaa00, 1.5, 3);
      dogLight.position.set(0, 0.8, 0.8);
      dogGroup.add(dogLight);

      // 3D Speech Bubble Sprite gắn vào chú chó
this.dogSpeechTexture = this.createSpeechBubbleTexture("Gâu gâu! Chào bạn đến với Techshop! 🐶");
const spriteMat = new THREE.SpriteMaterial({
  map: this.dogSpeechTexture,
  transparent: true,
  depthWrite: false,
  depthTest: false,   // luôn hiển thị phía trước
  fog: false          // không bị sương mù làm mờ
});
const speechSprite = new THREE.Sprite(spriteMat);
speechSprite.center.set(0.5, 0);        // neo ở đáy, đuôi bong bóng trỏ vào chó
speechSprite.scale.set(2.8, 1.4, 1);    // đúng tỉ lệ 2:1 của canvas
speechSprite.position.set(0, 1.0, 0);
speechSprite.renderOrder = 999;
this.dogSpeechSprite = markRaw(speechSprite);
dogGroup.add(speechSprite);    
      this.scene.add(dogGroup);
      this.robotDog = markRaw(dogGroup);
      this.dogWander = markRaw({ target: new THREE.Vector3(2, 0, 32), nextChange: 0, yaw: 0 });

      // Rotate messages automatically
      this.dogMsgTimer = setInterval(() => {
        if (!this.started || this.isUnmounted) return;
        const randomIdx = Math.floor(Math.random() * this.dogMessages.length);
        this.updateDogSpeech(this.dogMessages[randomIdx]);
      }, 6500);
    },
    buildWallGallery() {
      this.wallPhotos.forEach((photo, index) => this.addWallPhoto(photo, index));
      this.addWallStoryPanels();
    },
    addWallPhoto(photo, index) {
      const group = new THREE.Group();
      group.position.set(photo.side === "left" ? -21.25 : 21.25, 4.25, photo.z);
      group.rotation.y = photo.side === "left" ? Math.PI / 2 : -Math.PI / 2;
      this.scene.add(group);

      this.addBox(group, 5.15, 3.32, 0.2, 0x060f1e, 0, 0, 0, { roughness: 0.2, metalness: 0.85 });
      this.addBox(group, 4.95, 3.12, 0.08, 0x253b5c, 0, 0, 0.12, { roughness: 0.12, metalness: 0.9 });
      const photoMaterial = new THREE.MeshBasicMaterial({ color: 0x152844 });
      const photoMesh = new THREE.Mesh(new THREE.PlaneGeometry(4.72, 2.55), photoMaterial);
      photoMesh.position.set(0, 0.18, 0.17);
      photoMesh.userData.exhibitId = photo.exhibit;
      group.add(photoMesh);
      this.interactionTargets.push(photoMesh);

      this.addGlowBar(group, 0, 1.58, 0.19, 4.95, 0.04, 0.05, index % 2 ? 0xb892ff : 0x5ee1ff, 0.99);
      this.addGlowBar(group, 0, -1.58, 0.19, 4.95, 0.04, 0.05, 0x224a7a, 0.98);
      this.addGlowBar(group, -2.48, 0, 0.19, 0.04, 3.12, 0.05, 0x224a7a, 0.98);
      this.addGlowBar(group, 2.48, 0, 0.19, 0.04, 3.12, 0.05, 0x224a7a, 0.98);

      const caption = this.makeTextSprite(photo.title, photo.subtitle, index % 2 ? "#d4bcff" : "#5ef3ff", 4.7, 0.72);
      caption.position.set(0, -2.05, 0.18);
      group.add(caption);

      const light = new THREE.PointLight(index % 2 ? 0xb49eff : 0x5ef3ff, 1.0, 6);
      light.position.set(0, 0.1, 2.0);
      group.add(light);

      this.textureLoader.load(
        photo.image,
        texture => {
          if (this.isUnmounted) {
            texture.dispose();
            return;
          }
          texture.colorSpace = THREE.SRGBColorSpace;
          texture.anisotropy = Math.min(this.renderer?.capabilities?.getMaxAnisotropy?.() || 1, 8);
          photoMaterial.map = texture;
          photoMaterial.color.set(0xffffff);
          photoMaterial.needsUpdate = true;
        },
        undefined,
        () => {
          photoMaterial.color.set(0x1d365c);
          photoMaterial.needsUpdate = true;
        }
      );
    },
    addWallStoryPanels() {
      const stories = [
        { side: "left", z: 30, title: "OUR STORY", sub: "A BETTER WAY TO EXPLORE TECH", color: "#5ef3ff" },
        { side: "right", z: -1, title: "BUILT FOR CREATIVITY", sub: "HARDWARE MEETS IMAGINATION", color: "#d9bcff" },
        { side: "left", z: -24, title: "THE DETAILS MATTER", sub: "EVERY COMPONENT HAS A ROLE", color: "#5ef3ff" },
        { side: "right", z: -52, title: "FIND YOUR NEXT SETUP", sub: "DISCOVER · COMPARE · CHOOSE", color: "#ffd085" }
      ];
      stories.forEach(story => {
        const sign = this.makeTextSprite(story.title, story.sub, story.color, 5.4, 1.3);
        sign.position.set(story.side === "left" ? -20.65 : 20.65, 6.25, story.z);
        sign.rotation.y = story.side === "left" ? Math.PI / 2 : -Math.PI / 2;
        this.scene.add(sign);
      });
    },
    buildCeilingDetails() {
      const rings = [
        { z: 22, color: 0x5ee1ff }, { z: 0, color: 0xb892ff },
        { z: -22, color: 0x3bf3d4 }, { z: -44, color: 0xffb878 }, { z: -64, color: 0xcaa6ff }
      ];
      rings.forEach((entry, index) => {
        const ring = new THREE.Mesh(
          new THREE.TorusGeometry(3.1 + (index % 2) * 0.45, 0.03, 8, 100),
          new THREE.MeshBasicMaterial({ color: entry.color, transparent: true, opacity: 0.8 })
        );
        ring.position.set(0, 7.72, entry.z);
        ring.rotation.x = Math.PI / 2;
        this.scene.add(ring);
        const inner = new THREE.Mesh(
          new THREE.TorusGeometry(2.55, 0.015, 6, 88),
          new THREE.MeshBasicMaterial({ color: 0xb4d4ff, transparent: true, opacity: 0.5 })
        );
        inner.position.copy(ring.position);
        inner.rotation.x = Math.PI / 2;
        this.scene.add(inner);
        const drop = new THREE.PointLight(entry.color, 1.3, 10);
        drop.position.set(0, 6.25, entry.z);
        this.scene.add(drop);
      });
    },
    addParticles() {
      const positions = [];
      for (let i = 0; i < 300; i++) {
        positions.push((Math.random() - 0.5) * 40, Math.random() * 7.5 + 0.35, (Math.random() - 0.5) * 112 - 16);
      }
      const geometry = new THREE.BufferGeometry();
      geometry.setAttribute("position", new THREE.Float32BufferAttribute(positions, 3));
      const particles = new THREE.Points(
        geometry,
        new THREE.PointsMaterial({ color: 0x8bdfff, size: 0.035, transparent: true, opacity: 0.7, depthWrite: false })
      );
      this.scene.add(particles);
    },
    enterMuseum() {
      this.started = true;
      this.storeInfoOpen = false;
      this.$nextTick(() => this.controls?.lock());
    },
    onCanvasClick() {
      if (!this.started || this.activeExhibit || !this.controls) return;
      if (this.hoveredExhibit && this.controls.isLocked) {
        this.openExhibit(this.hoveredExhibit);
        return;
      }
      if (!this.controls.isLocked) this.controls.lock();
    },
    onKeyDown(event) {
      if (["KeyW", "KeyA", "KeyS", "KeyD", "ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight", "Space"].includes(event.code)) {
        event.preventDefault();
      }
      this.keys[event.code] = true;
      if (event.code === "KeyE" && this.started && this.hoveredExhibit && !this.activeExhibit) this.openExhibit(this.hoveredExhibit);
      if (event.code === "Escape" && this.activeExhibit) this.closeExhibit();
      if (event.code === "KeyM" && this.started) this.storeInfoOpen = !this.storeInfoOpen;
    },
    onKeyUp(event) {
      this.keys[event.code] = false;
    },
    clearKeys() {
      this.keys = {};
    },
    openExhibit(id) {
      if (!this.exhibits.some(item => item.id === id)) return;
      this.activeExhibit = id;
      this.hoveredExhibit = null;
      this.storeInfoOpen = false;
      if (!this.visitedExhibits.includes(id)) this.visitedExhibits.push(id);
      this.controls?.unlock();
    },
    closeExhibit() {
      this.activeExhibit = null;
      this.hoveredExhibit = null;
    },
    animate() {
      this.animationFrame = requestAnimationFrame(this.animate.bind(this));
      if (!this.renderer || !this.scene || !this.camera) return;
      const delta = Math.min(this.clock.getDelta(), 0.05);
      const time = performance.now() * 0.001;

      if (this.controls?.isLocked && !this.activeExhibit) {
        const movingForward = this.keys.KeyW || this.keys.ArrowUp;
        const movingBack = this.keys.KeyS || this.keys.ArrowDown;
        const movingLeft = this.keys.KeyA || this.keys.ArrowLeft;
        const movingRight = this.keys.KeyD || this.keys.ArrowRight;
        const isMoving = movingForward || movingBack || movingLeft || movingRight;
        const targetSpeed = isMoving ? 7.0 : 0;
        
        const direction = new THREE.Vector3(
          (movingRight ? 1 : 0) - (movingLeft ? 1 : 0),
          0,
          (movingForward ? 1 : 0) - (movingBack ? 1 : 0)
        );
        if (direction.lengthSq() > 0) direction.normalize().multiplyScalar(targetSpeed);
        
        this.velocity.x = THREE.MathUtils.damp(this.velocity.x, direction.x, 15, delta);
        this.velocity.z = THREE.MathUtils.damp(this.velocity.z, direction.z, 15, delta);
        if (Math.abs(this.velocity.z) > 0.01) this.controls.moveForward(this.velocity.z * delta);
        if (Math.abs(this.velocity.x) > 0.01) this.controls.moveRight(this.velocity.x * delta);

        // Jump mechanics
        if (this.keys.Space && this.canJump && !this.isJumping) {
          this.verticalVelocity = 5.8;
          this.isJumping = true;
          this.canJump = false;
        }

        this.verticalVelocity -= 16 * delta;
        this.camera.position.y += this.verticalVelocity * delta;

        if (this.camera.position.y <= 1.68) {
          this.camera.position.y = 1.68;
          this.verticalVelocity = 0;
          this.isJumping = false;
          this.canJump = true;
        }

        this.camera.position.x = THREE.MathUtils.clamp(this.camera.position.x, -19.6, 19.6);
        this.camera.position.z = THREE.MathUtils.clamp(this.camera.position.z, -71.5, 39.5);

        // Robot Dog Follow & Running Leg Animation
       // Robot Dog Companion follow AI
  
// Robot Dog Companion: tự chạy lang thang quanh người chơi (không phụ thuộc hướng nhìn)
if (this.robotDog && this.dogWander) {
  const camPos = this.camera.position;
  const wander = this.dogWander;
  const dog = this.robotDog;

  const distToPlayer = Math.hypot(camPos.x - dog.position.x, camPos.z - dog.position.z);
  const distToTarget = Math.hypot(wander.target.x - dog.position.x, wander.target.z - dog.position.z);
  const targetToPlayer = Math.hypot(camPos.x - wander.target.x, camPos.z - wander.target.z);

  // Chọn điểm mới khi: hết giờ, hoặc người chơi đi xa khỏi điểm cũ
  if (time > wander.nextChange || targetToPlayer > 16) {
    const angle = Math.random() * Math.PI * 2;      // hướng ngẫu nhiên theo thế giới
    const radius = 6 + Math.random() * 7;           // cách người chơi 6 → 13
    wander.target.set(
      THREE.MathUtils.clamp(camPos.x + Math.cos(angle) * radius, -19, 19),
      0,
      THREE.MathUtils.clamp(camPos.z + Math.sin(angle) * radius, -71, 39)
    );
    wander.nextChange = time + 5 + Math.random() * 5; // 5 → 10 giây
  }

  // Chạy tới điểm đó với tốc độ cố định; bị bỏ quá xa thì chạy nhanh hơn
  const prevX = dog.position.x;
  const prevZ = dog.position.z;
  let isDogMoving = false;
  if (distToTarget > 0.4) {
    const speed = distToPlayer > 18 ? 11 : 4.5;
    const step = Math.min(distToTarget, speed * delta);
    dog.position.x += ((wander.target.x - prevX) / distToTarget) * step;
    dog.position.z += ((wander.target.z - prevZ) / distToTarget) * step;
    isDogMoving = true;
  }
  dog.position.y = 0;

  // Quay mặt theo hướng chạy; đứng yên thì quay nhìn người chơi
  const desiredYaw = isDogMoving
    ? Math.atan2(dog.position.x - prevX, dog.position.z - prevZ)
    : Math.atan2(camPos.x - dog.position.x, camPos.z - dog.position.z);
  let diff = desiredYaw - wander.yaw;
  diff = Math.atan2(Math.sin(diff), Math.cos(diff));
  wander.yaw += diff * Math.min(1, delta * 8);
  dog.rotation.y = wander.yaw;

  // Chân chạy khi di chuyển
  if (this.dogLegs) {
    this.dogLegs.forEach(legObj => {
      if (isDogMoving) {
        const sign = legObj.index % 2 === 0 ? 1 : -1;
        legObj.mesh.rotation.x = Math.sin(time * 18 + legObj.index * Math.PI) * 0.5 * sign;
      } else {
        legObj.mesh.rotation.x = THREE.MathUtils.damp(legObj.mesh.rotation.x, 0, 10, delta);
      }
    });
  }
}

        this.raycaster.setFromCamera(this.centerPoint, this.camera);
        const hits = this.raycaster.intersectObjects(this.interactionTargets, false);
        const target = hits.find(hit => hit.distance < 8.5 && hit.object.userData.exhibitId);
        const nextHover = target ? target.object.userData.exhibitId : null;
        if (nextHover !== this.hoveredExhibit) this.hoveredExhibit = nextHover;

        const z = this.camera.position.z;
        if (z > 27) this.currentZone = "ENTRANCE & RECEPTION";
        else if (z > 9) this.currentZone = "01 / THE BEGINNING";
        else if (z > -5) this.currentZone = "02 / PERFORMANCE AGE";
        else if (z > -19) this.currentZone = "03 / CONNECTED ERA";
        else if (z > -33) this.currentZone = "04 / HARDWARE GALLERY";
        else if (z > -47) this.currentZone = "05 / CREATOR STUDIO";
        else if (z > -60) this.currentZone = "06 / TECHSHOP SHOWROOM";
        else this.currentZone = "07 / FUTURE LAB";
      }

      this.interactionTargets?.forEach((object, index) => {
        if (object.geometry?.type === "IcosahedronGeometry") {
          object.position.y = 2.8 + Math.sin(time * 1.8 + index) * 0.12;
          object.rotation.y = time * 0.6 + index;
        }
      });
      this.renderer.render(this.scene, this.camera);
    },
    onResize() {
      const host = this.$refs.viewport;
      if (!host || !this.camera || !this.renderer) return;
      this.camera.aspect = Math.max(host.clientWidth, 1) / Math.max(host.clientHeight, 1);
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(host.clientWidth, host.clientHeight);
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 1.5));
    },
    goHome() {
      this.$router.push("/trang-chu");
    },
    goProducts() {
      const candidates = ["/san-pham", "/client/san-pham", "/cua-hang"];
      const route = candidates.find(path => this.$router.resolve(path).matched.length > 0);
      if (route) this.$router.push(route);
      else window.alert("Chưa xác định được đường dẫn trang sản phẩm. Hãy kiểm tra src/router/index.js.");
    }
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Be+Vietnam+Pro:wght@400;500;600;700;800;900&family=Space+Grotesk:wght@400;500;600;700&display=swap');

:global(html), :global(body) {
  margin: 0;
  background: #040710;
  scroll-behavior: smooth;
}

.museum-page {
  --cyan: #5ee1ff;
  --blue: #2575ff;
  --purple: #b892ff;

  position: fixed;
  inset: 0;
  z-index: 99999;

  width: 100vw;
  height: 100vh;
  height: 100dvh;
  min-height: 0;

  overflow: hidden;
  isolation: isolate;
  color: #f5f8ff;
  background: #040710;
  font-family: 'Be Vietnam Pro', sans-serif;
}

:global(body:has(.museum-page)) {
  overflow: hidden !important;
}

.museum-page *, .museum-page *::before, .museum-page *::after { box-sizing: border-box; }
.museum-page button { font: inherit; }
.scene-viewport { position: absolute; inset: 0; z-index: 0; background: #040710; }
.scene-viewport canvas { display: block; width: 100%; height: 100%; outline: none; }
.scene-vignette {
  position: absolute; inset: 0; z-index: 1; pointer-events: none;
  background: linear-gradient(180deg, rgba(2,5,10,.88), transparent 20%, transparent 72%, rgba(2,5,10,.92)),
    radial-gradient(ellipse at center, transparent 25%, rgba(2,5,10,.55) 100%);
}
.scene-grain {
  position: absolute; inset: 0; z-index: 2; opacity: .06; pointer-events: none;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 180 180' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='.4'/%3E%3C/svg%3E");
}

/* Header */
.museum-header {
  position: absolute; z-index: 10; top: 0; left: 0; right: 0;
  display: flex; align-items: center; justify-content: space-between; gap: 22px;
  padding: 19px 4.4%; border-bottom: 1px solid rgba(120,175,235,.2);
  background: linear-gradient(180deg, rgba(3,8,18,.94), rgba(3,8,18,.4));
  backdrop-filter: blur(18px);
}
.brand { display: inline-flex; align-items: center; gap: 10px; padding: 0; border: 0; color: #f3f7ff; background: transparent; cursor: pointer; }
.brand-name { font: 800 18px 'Space Grotesk', sans-serif; letter-spacing: -.9px; }
.brand-name > span { color: var(--cyan); }
.brand small { margin-left: 7px; padding-left: 13px; border-left: 1px solid rgba(150,190,235,.3); color: #728fb5; font-size: 7px; font-weight: 900; letter-spacing: 1.5px; }
.brand-symbol { display: grid; place-items: center; width: 36px; height: 36px; border: 1px solid rgba(94,225,255,.6); border-radius: 10px; color: #030b16; background: linear-gradient(140deg,#7ef0ff,#2575ff); box-shadow: 0 0 30px rgba(37,117,255,.4); transition: transform .3s; }
.brand:hover .brand-symbol { transform: rotate(-10deg) scale(1.08); }
.header-center { display: flex; align-items: center; gap: 9px; color: #7d9cc4; font-size: 8px; font-weight: 800; letter-spacing: 1.35px; }
.header-center b { color: #3b5c88; }
.live-dot { display: inline-block; width: 6px; height: 6px; flex: 0 0 6px; border-radius: 50%; background: #3bf3cc; box-shadow: 0 0 14px #3bf3cc; animation: blink 1.8s infinite; }
.header-actions { display: flex; align-items: center; gap: 9px; }
.header-location, .header-store { display: inline-flex; align-items: center; gap: 10px; min-height: 39px; padding: 0 13px; border: 1px solid rgba(94,225,255,.45); border-radius: 7px; color: #dff8ff; background: rgba(20,80,135,.25); font-size: 8px; font-weight: 900; letter-spacing: .65px; cursor: pointer; transition: .25s; }
.header-location { color: #a4c4ee; border-color: rgba(125,165,220,.25); background: rgba(5,13,26,.6); }
.header-location:hover, .header-store:hover { border-color: var(--cyan); background: rgba(28,115,185,.45); transform: translateY(-2px); box-shadow: 0 8px 30px rgba(40,145,230,.25); }

/* Store address popover */
.store-popover {
  position: absolute; z-index: 15; top: 92px; right: 4.4%; width: min(390px, 92vw);
  padding: 19px; border: 1px solid rgba(94,225,255,.4); border-radius: 13px;
  background: linear-gradient(145deg, rgba(8,20,40,.99), rgba(3,9,20,.99));
  box-shadow: 0 30px 80px rgba(0,0,0,.6); backdrop-filter: blur(20px); animation: popIn .24s ease both;
}
.store-popover-head { display: flex; align-items: center; gap: 11px; padding-bottom: 15px; border-bottom: 1px solid rgba(125,165,220,.18); }
.store-popover-head > div { display: grid; gap: 4px; }
.store-popover-head small, .store-popover-row small { color: #6285b5; font-size: 7px; font-weight: 900; letter-spacing: 1.2px; }
.store-popover-head strong { color: #f0f7ff; font-size: 13px; }
.store-popover-icon { display: grid; place-items: center; width: 42px; height: 42px; border: 1px solid rgba(94,225,255,.38); border-radius: 11px; color: #5ef3ff; background: rgba(40,125,190,.2); font-size: 17px; }
.store-popover-head button { display: grid; place-items: center; width: 29px; height: 29px; margin-left: auto; border: 1px solid rgba(135,175,220,.25); border-radius: 7px; color: #94b5e0; background: transparent; cursor: pointer; }
.store-popover-row { display: flex; align-items: flex-start; gap: 12px; padding: 13px 0; border-bottom: 1px solid rgba(125,165,220,.12); }
.store-popover-row > i { width: 17px; margin-top: 3px; color: var(--cyan); text-align: center; }
.store-popover-row div { display: grid; gap: 5px; }
.store-popover-row strong { color: #def0ff; font-size: 10px; line-height: 1.65; }
.store-map-link { display: flex; align-items: center; justify-content: space-between; margin-top: 15px; padding: 12px 13px; border: 1px solid rgba(94,225,255,.45); border-radius: 7px; color: #dff8ff; background: rgba(28,115,185,.3); font-size: 8px; font-weight: 900; letter-spacing: .8px; text-decoration: none; transition: .2s; }
.store-map-link:hover { background: rgba(28,115,185,.55); }
.store-demo-note { margin: 12px 0 0; color: #6282a8; font-size: 8px; line-height: 1.7; }

/* Intro */
.intro-screen {
  position: absolute; z-index: 4; inset: 0; display: flex; align-items: center;
  padding: 105px 6.5% 85px; overflow: hidden;
  background: linear-gradient(90deg, rgba(2,5,12,.98) 0%, rgba(3,8,18,.94) 39%, rgba(3,8,18,.48) 72%, rgba(3,8,18,.25) 100%);
  animation: introFade .8s ease both;
}
.intro-backdrop { position: absolute; inset: 0; background: radial-gradient(ellipse at 77% 45%, rgba(20,90,175,.3), transparent 40%), radial-gradient(ellipse at 22% 80%, rgba(90,55,185,.18), transparent 38%); pointer-events: none; }
.intro-content { position: relative; z-index: 3; width: min(650px, 55%); }
.intro-kicker { display: flex; align-items: center; gap: 10px; color: #7db2df; font-size: 9px; font-weight: 900; letter-spacing: 2px; }
.intro-overline { margin: 36px 0 7px; color: #628bb8; font-size: 10px; font-weight: 900; letter-spacing: 4px; }
.intro-content h1 { margin: 0; color: #f0f7ff; font: 700 clamp(63px, 7.5vw, 108px)/.92 'Space Grotesk', sans-serif; letter-spacing: -6px; text-shadow: 0 12px 65px rgba(0,0,0,.5); }
.intro-content h1 > span { color: var(--cyan); }
.intro-content h1 em { display: inline-block; margin-top: 14px; color: transparent; font-size: .42em; font-style: normal; font-weight: 700; letter-spacing: 6px; background: linear-gradient(90deg,#b0e4ff,#7899ff 58%,#d0abff); background-clip: text; -webkit-background-clip: text; }
.intro-description { max-width: 570px; margin: 26px 0 0; color: #94aeca; font-size: 12px; line-height: 2.05; }
.intro-stats { display: flex; gap: 31px; margin-top: 26px; }
.intro-stats div { display: grid; gap: 4px; padding-right: 27px; border-right: 1px solid rgba(120,165,220,.2); }
.intro-stats div:last-child { border: 0; }
.intro-stats strong { color: #f0f7ff; font: 700 25px 'Space Grotesk', sans-serif; }
.intro-stats span { color: #5e80aa; font-size: 7px; font-weight: 900; letter-spacing: 1.2px; }
.enter-button { display: inline-flex; align-items: center; gap: 14px; min-width: 270px; margin-top: 32px; padding: 11px 17px 11px 11px; border: 1px solid rgba(94,225,255,.65); border-radius: 9px; color: white; background: linear-gradient(110deg,rgba(22,95,160,.6),rgba(50,55,150,.45)); box-shadow: 0 12px 40px rgba(28,115,200,.3), inset 0 0 25px rgba(94,225,255,.15); text-align: left; cursor: pointer; transition: .28s; }
.enter-button:hover { transform: translateY(-4px); border-color: #8ef0ff; box-shadow: 0 18px 50px rgba(28,115,200,.42), inset 0 0 30px rgba(94,225,255,.25); }
.enter-icon { display: grid; place-items: center; width: 43px; height: 43px; border-radius: 7px; color: #030d1a; background: linear-gradient(140deg,#7ef0ff,#3d8aff); box-shadow: 0 0 22px rgba(94,225,255,.4); }
.enter-button > span:nth-child(2) { display: grid; gap: 4px; }
.enter-button strong { font-size: 9px; font-weight: 900; letter-spacing: 1px; }
.enter-button small { color: #72b2e2; font-size: 7px; font-weight: 800; letter-spacing: 1.2px; }
.enter-arrow { margin-left: auto; color: #7ef0ff; transition: transform .25s; }
.enter-button:hover .enter-arrow { transform: translateX(5px); }
.intro-foot { display: flex; align-items: center; gap: 13px; margin-top: 32px; color: #6a8bb8; font-size: 8px; font-weight: 800; letter-spacing: .9px; }
.intro-foot span { display: flex; gap: 6px; }
.intro-foot b { color: #d0e7ff; }
.intro-foot > i { width: 3px; height: 3px; border-radius: 50%; background: #3b72b2; }
.intro-photo-stack { position: absolute; z-index: 2; top: 17%; right: 7%; width: 39%; height: 67%; }
.intro-photo { position: absolute; overflow: hidden; border: 1px solid rgba(170,210,255,.35); border-radius: 12px; background: #11223b; box-shadow: 0 30px 80px rgba(0,0,0,.55); }
.intro-photo img { display: block; width: 100%; height: 100%; object-fit: cover; filter: saturate(.9) contrast(1.08); }
.intro-photo-large { top: 3%; right: 3%; width: 84%; height: 76%; transform: rotate(3deg); }
.intro-photo-small { left: 0; bottom: 1%; width: 44%; height: 41%; transform: rotate(-7deg); border-width: 5px; border-color: rgba(4,12,25,.95); }
.intro-photo > span { position: absolute; right: 13px; bottom: 12px; color: #e8f5ff; font: 800 7px 'Space Grotesk', sans-serif; letter-spacing: 1.4px; text-shadow: 0 2px 8px #000; }
.intro-photo-caption { position: absolute; right: 0; bottom: -4%; color: #6e92be; font: 800 8px/1.9 'Space Grotesk', sans-serif; letter-spacing: 2px; text-align: right; }
.intro-photo-caption b { color: #b0e0ff; }
.intro-orbit { position: absolute; z-index: 1; width: 650px; height: 650px; border: 1px solid rgba(55,150,230,.2); border-radius: 50%; transform: rotateX(68deg) rotateZ(-25deg); pointer-events: none; }
.orbit-a { top: 8%; right: -5%; animation: slowOrbit 55s linear infinite; }
.orbit-b { top: 19%; right: 0; width: 520px; height: 520px; border-style: dashed; border-color: rgba(130,100,255,.28); animation: slowOrbit 42s linear infinite reverse; }
.orbit-c { top: 31%; right: 7%; width: 380px; height: 380px; border-color: rgba(75,205,255,.16); }
.intro-side-note { position: absolute; right: 6.5%; bottom: 28px; left: 6.5%; display: flex; justify-content: space-between; gap: 15px; color: #52729a; font: 800 7px 'Space Grotesk', sans-serif; letter-spacing: 1.6px; }
.intro-index { position: absolute; top: 22%; right: 5.2%; color: rgba(105,165,225,.45); font: 700 67px 'Space Grotesk', sans-serif; letter-spacing: -5px; }
.intro-index span { color: rgba(105,165,225,.7); font-size: 19px; letter-spacing: 0; }

/* HUD */
.top-readout { position: absolute; z-index: 5; top: 91px; left: 4.5%; display: flex; align-items: center; gap: 12px; color: #8cadc8; font: 700 8px 'Space Grotesk', sans-serif; letter-spacing: 1.5px; }
.readout-divider { width: 28px; height: 1px; background: #2d72be; }
.visitor-hud { position: absolute; z-index: 5; top: 125px; left: 4.5%; display: flex; flex-direction: column; gap: 16px; }
.visitor-card { width: 205px; padding: 14px 15px; border: 1px solid rgba(120,170,230,.28); border-radius: 10px; background: rgba(4,10,22,.7); backdrop-filter: blur(14px); box-shadow: 0 14px 45px rgba(0,0,0,.3); }
.hud-caption { display: block; color: #6a8dbb; font-size: 7px; font-weight: 900; letter-spacing: 1.7px; }
.visitor-card strong { display: block; margin-top: 6px; color: #f0f7ff; font: 700 15px 'Space Grotesk', sans-serif; letter-spacing: 1px; }
.visit-progress { height: 3px; margin: 12px 0 8px; overflow: hidden; border-radius: 10px; background: rgba(110,150,200,.2); }
.visit-progress span { display: block; height: 100%; border-radius: 10px; background: linear-gradient(90deg,#2575ff,#3bf3cc); box-shadow: 0 0 14px #33b5ff; transition: width .45s ease; }
.visitor-card small { color: #7292bc; font-size: 7px; font-weight: 800; letter-spacing: 1px; }
.control-help { display: grid; gap: 7px; color: #7292bc; font-size: 7px; font-weight: 800; letter-spacing: 1px; }
.control-help span { display: flex; align-items: center; gap: 5px; }
.control-help kbd { display: inline-grid; place-items: center; min-width: 19px; height: 18px; padding: 0 5px; border: 1px solid rgba(135,175,230,.32); border-radius: 4px; color: #cef0ff; background: rgba(8,20,40,.9); font: 700 7px 'Space Grotesk', sans-serif; }
.crosshair { position: absolute; z-index: 6; top: 50%; left: 50%; width: 20px; height: 20px; transform: translate(-50%,-50%); pointer-events: none; opacity: .9; }
.crosshair span, .crosshair::before, .crosshair::after { position: absolute; display: block; background: #f0f8ff; content: ''; }
.crosshair span { top: 8px; left: 8px; width: 4px; height: 4px; border-radius: 50%; box-shadow: 0 0 14px var(--cyan); }
.crosshair::before { top: 9px; left: 0; width: 5px; height: 1px; }
.crosshair::after { top: 9px; right: 0; width: 5px; height: 1px; }
.crosshair i { position: absolute; top: 0; left: 9px; width: 1px; height: 5px; background: #f0f8ff; box-shadow: 0 15px #f0f8ff; }
.crosshair.focused span { background: var(--cyan); box-shadow: 0 0 20px var(--cyan); }
.interact-prompt { position: absolute; z-index: 7; top: calc(50% + 30px); left: 50%; display: grid; grid-template-columns: 32px auto; align-items: center; gap: 2px 9px; min-width: 220px; padding: 11px 14px; border: 1px solid rgba(94,225,255,.5); border-radius: 8px; background: rgba(3,10,22,.96); box-shadow: 0 12px 40px rgba(0,0,0,.4); transform: translateX(-50%); backdrop-filter: blur(14px); animation: promptIn .2s ease both; }
.prompt-key { grid-row: span 2; display: grid; place-items: center; width: 29px; height: 29px; border: 1px solid rgba(94,225,255,.65); border-radius: 5px; color: #7ef0ff; font: 800 13px 'Space Grotesk', sans-serif; }
.interact-prompt > span:nth-child(2) { color: #6d93bc; font-size: 7px; font-weight: 900; letter-spacing: 1px; }
.interact-prompt b { color: #f0f7ff; font-size: 9px; }
.exhibit-navigation { position: absolute; z-index: 8; top: 50%; right: 4.5%; display: grid; gap: 8px; transform: translateY(-50%); }
.nav-label { margin-bottom: 3px; color: #6a8bb8; font-size: 6px; font-weight: 900; letter-spacing: 1.2px; text-align: center; }
.exhibit-navigation button { display: grid; place-items: center; gap: 3px; width: 43px; height: 46px; border: 1px solid rgba(135,175,230,.28); border-radius: 8px; color: #7d9dc9; background: rgba(5,13,26,.8); cursor: pointer; backdrop-filter: blur(12px); transition: .22s; }
.exhibit-navigation button span { font: 700 7px 'Space Grotesk', sans-serif; }
.exhibit-navigation button i { font-size: 13px; }
.exhibit-navigation button:hover, .exhibit-navigation button.selected { border-color: #5ee1ff; color: #e6ffff; background: rgba(28,115,185,.4); box-shadow: 0 0 25px rgba(45,180,250,.25); transform: translateX(-3px); }
.exhibit-navigation button.visited::after { width: 4px; height: 4px; border-radius: 50%; background: #3bf3cc; box-shadow: 0 0 8px #3bf3cc; content: ''; }
.bottom-hud { position: absolute; z-index: 7; right: 4.5%; bottom: 51px; left: 4.5%; display: flex; align-items: flex-end; justify-content: space-between; gap: 20px; pointer-events: none; }
.bottom-hud > div, .bottom-hud > button { pointer-events: auto; }
.bottom-current { display: grid; gap: 6px; }
.bottom-kicker { color: #6a8bb8; font-size: 7px; font-weight: 900; letter-spacing: 1.5px; }
.bottom-hud strong { color: #eef5ff; font: 700 12px 'Space Grotesk', sans-serif; letter-spacing: .6px; }
.bottom-center { display: flex; align-items: center; gap: 9px; color: #6a8bb8; font-size: 7px; font-weight: 900; letter-spacing: 1px; }
.bottom-center b { color: #2d72be; }
.sound-wave { display: flex; align-items: center; gap: 2px; height: 14px; }
.sound-wave i { width: 2px; height: 5px; border-radius: 5px; background: #5ee1ff; animation: wave .8s ease-in-out infinite alternate; }
.sound-wave i:nth-child(2) { height: 10px; animation-delay: .2s; }
.sound-wave i:nth-child(3) { height: 7px; animation-delay: .4s; }
.sound-wave i:nth-child(4) { height: 12px; animation-delay: .1s; }
.sound-wave i:nth-child(5) { height: 6px; animation-delay: .3s; }
.bottom-hud > button { display: flex; align-items: center; gap: 13px; padding: 12px 15px; border: 1px solid rgba(94,225,255,.5); border-radius: 7px; color: #dff8ff; background: rgba(14,70,118,.5); font-size: 8px; font-weight: 900; letter-spacing: .8px; cursor: pointer; transition: .25s; }
.bottom-hud > button:hover { background: rgba(28,115,185,.55); border-color: #7ef0ff; }
.museum-disclaimer { position: absolute; z-index: 8; bottom: 13px; left: 50%; color: rgba(100,130,165,.75); font-size: 6px; font-weight: 800; letter-spacing: 1.35px; white-space: nowrap; transform: translateX(-50%); pointer-events: none; }

/* Exhibit detail */
.artifact-overlay { position: absolute; z-index: 20; inset: 0; display: flex; align-items: center; justify-content: flex-end; padding: 88px 5% 58px; background: rgba(2,5,10,.75); backdrop-filter: blur(12px); animation: fadeIn .24s ease; }
.artifact-panel { display: grid; grid-template-columns: .82fr 1fr; width: min(1050px,100%); max-height: 100%; overflow: auto; border: 1px solid rgba(94,225,255,.4); border-radius: 16px; background: linear-gradient(145deg,rgba(8,19,38,.99),rgba(2,8,18,.99)); box-shadow: 0 40px 120px rgba(0,0,0,.7); animation: panelIn .34s cubic-bezier(.2,.8,.2,1); scrollbar-width: thin; scrollbar-color: #224a7a #050f1e; }
.panel-topline { grid-column: 1/-1; display: flex; align-items: center; justify-content: space-between; padding: 14px 19px; border-bottom: 1px solid rgba(125,165,220,.2); color: #76a2d4; font-size: 8px; font-weight: 900; letter-spacing: 1.4px; }
.panel-topline > span { display: flex; align-items: center; gap: 9px; }
.panel-topline i { color: #5ee1ff; }
.close-panel { display: grid; place-items: center; width: 31px; height: 31px; border: 1px solid rgba(140,180,225,.28); border-radius: 7px; color: #b8d2ef; background: rgba(255,255,255,.05); cursor: pointer; transition: .2s; }
.close-panel:hover { color: #fff; border-color: #5ee1ff; background: rgba(40,130,200,.25); transform: rotate(90deg); }
.artifact-visual { position: relative; display: grid; place-items: center; min-height: 420px; overflow: hidden; background: #0b182c; }
.artifact-visual img { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; filter: saturate(.88); }
.artifact-visual-shade { position: absolute; inset: 0; background: linear-gradient(180deg,rgba(2,6,14,.28),rgba(2,6,14,.78)),linear-gradient(90deg,rgba(6,16,30,.2),rgba(6,16,30,.12)); }
.visual-chip { position: relative; z-index: 1; display: grid; place-items: center; width: 118px; height: 118px; border: 1px solid rgba(110,225,255,.65); border-radius: 29px; color: #7ef0ff; background: rgba(12,48,80,.55); box-shadow: 0 0 90px rgba(45,170,245,.35),inset 0 0 45px rgba(110,225,255,.18); font-size: 49px; backdrop-filter: blur(12px); transform: rotate(-7deg); }
.artifact-visual-power .visual-chip { color: #dfcbff; border-color: rgba(190,155,255,.6); }
.artifact-visual-mobile .visual-chip { color: #5ff4d9; }
.artifact-visual-techshop .visual-chip { color: #ffda9f; }
.visual-corner { position: absolute; top: 20px; left: 20px; z-index: 2; color: #def0ff; font-size: 7px; font-weight: 900; letter-spacing: 1.5px; text-shadow: 0 2px 8px #000; }
.visual-year { position: absolute; right: 20px; bottom: 18px; z-index: 2; color: #eef6ff; font: 700 23px 'Space Grotesk',sans-serif; letter-spacing: -1px; text-shadow: 0 2px 10px #000; }
.artifact-copy { padding: 25px 27px 22px; }
.artifact-meta { display: flex; align-items: center; gap: 10px; color: #7ee5ff; font-size: 7px; font-weight: 900; letter-spacing: 1.4px; }
.meta-line { width: 24px; height: 1px; background: #3272b2; }
.artifact-copy h2 { margin: 16px 0 10px; color: #f5f9ff; font: 700 clamp(25px,3vw,36px)/1.16 'Space Grotesk',sans-serif; letter-spacing: -1.3px; }
.artifact-lead { margin: 0 0 12px; color: #d4e5f8; font-size: 11px; font-weight: 700; line-height: 1.9; }
.artifact-description { margin: 0; color: #94abcb; font-size: 10px; line-height: 1.95; }
.artifact-facts { display: grid; grid-template-columns: repeat(3,minmax(0,1fr)); gap: 8px; margin-top: 19px; }
.fact-item { display: grid; gap: 7px; min-width: 0; padding: 11px 9px; border: 1px solid rgba(110,165,225,.22); border-radius: 7px; background: rgba(255,255,255,.035); }
.fact-item span { color: #6286b5; font-size: 6px; font-weight: 900; letter-spacing: 1px; }
.fact-item strong { color: #e6f2ff; font-size: 8px; line-height: 1.5; overflow-wrap: anywhere; }
.artifact-gallery { margin-top: 20px; }
.mini-gallery-heading { display: flex; justify-content: space-between; gap: 10px; margin-bottom: 9px; color: #7ee5ff; font-size: 7px; font-weight: 900; letter-spacing: 1.3px; }
.mini-gallery-heading small { color: #587c9f; font-size: 6px; }
.mini-gallery-grid { display: grid; grid-template-columns: repeat(2,minmax(0,1fr)); gap: 8px; }
.mini-gallery-grid figure { position: relative; height: 88px; margin: 0; overflow: hidden; border: 1px solid rgba(115,170,225,.25); border-radius: 6px; background: #101f36; }
.mini-gallery-grid img { display: block; width: 100%; height: 100%; object-fit: cover; opacity: .88; transition: transform .5s,opacity .3s; }
.mini-gallery-grid figure:hover img { opacity: 1; transform: scale(1.07); }
.mini-gallery-grid figcaption { position: absolute; right: 0; bottom: 0; left: 0; padding: 18px 8px 7px; color: #f0f7ff; background: linear-gradient(transparent,rgba(2,6,14,.92)); font-size: 7px; font-weight: 800; }
.artifact-location { display: flex; align-items: center; gap: 11px; margin-top: 17px; padding: 12px; border: 1px solid rgba(94,225,255,.32); border-radius: 8px; background: rgba(28,115,185,.14); }
.location-icon { display: grid; place-items: center; width: 35px; height: 35px; flex: 0 0 35px; border-radius: 9px; color: #7ef0ff; background: rgba(50,140,210,.25); }
.location-copy { display: grid; gap: 4px; min-width: 0; }
.location-copy span { color: #6ba8dc; font-size: 6px; font-weight: 900; letter-spacing: 1px; }
.location-copy strong { color: #eef6ff; font-size: 9px; line-height: 1.5; }
.location-copy small { color: #7296bc; font-size: 7px; }
.artifact-location a { margin-left: auto; padding: 9px 10px; border: 1px solid rgba(94,225,255,.38); border-radius: 5px; color: #d4f8ff; font-size: 7px; font-weight: 900; text-decoration: none; white-space: nowrap; }
.artifact-quote { display: flex; gap: 11px; margin-top: 17px; padding: 12px 13px; border-left: 2px solid #5ee1ff; color: #a4c2e5; background: rgba(55,150,225,.09); font-size: 9px; font-style: italic; line-height: 1.8; }
.artifact-quote i { color: #5ee1ff; }
.panel-bottom { display: flex; align-items: center; justify-content: space-between; gap: 12px; margin-top: 17px; padding-top: 14px; border-top: 1px solid rgba(125,165,220,.2); }
.panel-bottom > span { color: #3bf3cc; font-size: 7px; font-weight: 900; letter-spacing: .7px; }
.panel-bottom button { display: flex; align-items: center; gap: 10px; padding: 10px 12px; border: 1px solid rgba(94,225,255,.4); border-radius: 6px; color: #e2f9ff; background: rgba(35,115,180,.2); font-size: 7px; font-weight: 900; letter-spacing: .6px; cursor: pointer; transition: .2s; }
.panel-bottom button:hover { background: rgba(35,115,180,.4); }

/* Motion */
@keyframes blink { 50% { opacity: .35; box-shadow: 0 0 3px #3bf3cc; } }
@keyframes slowOrbit { to { transform: rotateX(68deg) rotateZ(336deg); } }
@keyframes wave { to { transform: scaleY(.4); opacity: .5; } }
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
@keyframes panelIn { from { opacity: 0; transform: translateX(22px) scale(.985); } to { opacity: 1; transform: translateX(0) scale(1); } }
@keyframes promptIn { from { opacity: 0; transform: translate(-50%,7px); } to { opacity: 1; transform: translate(-50%,0); } }
@keyframes introFade { from { opacity: 0; } to { opacity: 1; } }
@keyframes popIn { from { opacity: 0; transform: translateY(-7px) scale(.98); } to { opacity: 1; transform: translateY(0) scale(1); } }

/* Responsive */
@media (max-width: 1100px) {
  .header-center { display: none; }
  .intro-content { width: 58%; }
  .intro-photo-stack { right: 4%; width: 38%; }
  .artifact-overlay { justify-content: center; padding-right: 3%; padding-left: 3%; }
  .artifact-panel { width: min(960px,100%); }
}

@media (max-width: 760px) {
  .museum-page { min-height: 600px; }
  .museum-header { padding: 13px 4%; }
  .brand-name { font-size: 15px; }
  .brand-symbol { width: 31px; height: 31px; }
  .brand small { display: none; }
  .header-actions { gap: 6px; }
  .header-location, .header-store { min-height: 35px; padding: 0 9px; font-size: 7px; }
  .header-location span { display: none; }
  .intro-screen { align-items: center; padding: 100px 7% 85px; background: linear-gradient(90deg,rgba(2,5,10,.98),rgba(3,8,18,.88)); }
  .intro-content { width: min(100%,620px); }
  .intro-content h1 { font-size: clamp(53px,12vw,78px); letter-spacing: -4px; }
  .intro-content h1 em { font-size: .38em; letter-spacing: 3px; }
  .intro-description { max-width: 490px; font-size: 10px; }
  .intro-photo-stack { top: auto; right: 2%; bottom: 11%; width: 42%; height: 30%; opacity: .35; }
  .intro-photo-large { width: 100%; height: 100%; }
  .intro-photo-small, .intro-photo-caption, .intro-index, .orbit-b, .orbit-c { display: none; }
  .intro-stats { gap: 18px; }
  .intro-stats div { padding-right: 16px; }
  .intro-stats strong { font-size: 21px; }
  .intro-foot { flex-wrap: wrap; gap: 8px; font-size: 7px; }
  .intro-side-note { right: 7%; left: 7%; font-size: 6px; }
  .intro-side-note span:last-child { display: none; }
  .top-readout { top: 65px; left: 5%; font-size: 7px; }
  .visitor-hud { top: 120px; left: 5%; }
  .visitor-card { width: 160px; padding: 10px; }
  .control-help { display: none; }
  .exhibit-navigation { top: auto; right: 4%; bottom: 112px; display: flex; gap: 5px; transform: none; }
  .nav-label { display: none; }
  .exhibit-navigation button { width: 35px; height: 38px; }
  .bottom-hud { right: 4%; bottom: 48px; left: 4%; align-items: flex-end; gap: 8px; }
  .bottom-hud strong { max-width: 170px; font-size: 9px; }
  .bottom-center { display: none; }
  .bottom-hud > button { padding: 9px; font-size: 7px; }
  .museum-disclaimer { max-width: 90%; font-size: 5px; text-align: center; }
  .artifact-overlay { padding: 67px 3% 42px; }
  .artifact-panel { display: block; max-height: 100%; }
  .artifact-visual { min-height: 170px; }
  .visual-chip { width: 78px; height: 78px; border-radius: 20px; font-size: 34px; }
  .artifact-copy { padding: 19px 17px; }
  .artifact-copy h2 { font-size: 25px; }
  .artifact-description { font-size: 9px; }
  .artifact-facts { gap: 5px; }
  .fact-item { padding: 9px 7px; }
  .fact-item strong { font-size: 7px; }
  .artifact-location { align-items: flex-start; flex-wrap: wrap; }
  .artifact-location a { margin-left: 46px; }
  .panel-bottom { align-items: flex-start; flex-direction: column; }
  .panel-bottom button { width: 100%; justify-content: center; }
}

@media (max-width: 420px) {
  .header-store { gap: 6px; font-size: 6px; }
  .intro-kicker { font-size: 7px; letter-spacing: 1.3px; }
  .intro-content h1 { font-size: 49px; }
  .intro-description { font-size: 9px; }
  .intro-stats { gap: 12px; }
  .intro-stats div { padding-right: 10px; }
  .intro-stats strong { font-size: 18px; }
  .intro-stats span { font-size: 6px; }
  .enter-button { min-width: 0; width: 100%; }
  .exhibit-navigation button { width: 31px; height: 35px; }
  .exhibit-navigation button i { font-size: 11px; }
  .bottom-hud > button { font-size: 6px; gap: 6px; }
  .bottom-hud strong { max-width: 135px; font-size: 8px; }
  .store-popover { top: 65px; right: 3%; }
}

@media (prefers-reduced-motion: reduce) {
  .museum-page *, .museum-page *::before, .museum-page *::after { animation-duration: .01ms !important; animation-iteration-count: 1 !important; transition-duration: .01ms !important; }
}
</style>