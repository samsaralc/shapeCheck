<template>
  <div class="report">
    <div class="header">
      <div class="back" @click="goBack">
        <span></span>
      </div>
    </div>
    <div class="navigation">
      <div @click="changeRoute(1)">{{ $t("main.uploading") }}</div>
      <div @click="changeRoute(2)" class="active">{{ $t("report.title") }}</div>
      <div @click="changeRoute(3)">{{ $t("main.history") }}</div>
    </div>
    <div class="report-content" v-if="cateId == 32">
      <!-- 内容区域可滑动，现在使用垂直滚动 -->
      <div class="face-analysis">
        <!-- 滑动容器，改为垂直布局 -->
        <div class="analysis-slider">
          <!-- 第一个分析：整体分析（包含肤色、比例、面部留白） -->
          <div class="analysis-slide">
            <!-- 风格参考放到第一位 -->
            <div class="style-reference html2pic">
              <h2>风格参考</h2>
              <!-- 使用v-for循环遍历style_reference_list数组 -->
              <div
                v-for="(styleRef, index) in reportData?.style_reference_list ||
                []"
                :key="index"
                class="style-card"
              >
                <div class="style-content">
                  <!-- 左侧图片和颜色面板 -->
                  <div class="style-image-section" v-if="styleRef.imageUrl">
                    <img
                      :src="styleRef.imageUrl || '../assets/baseDeepPic.png'"
                      alt="风格参考"
                      class="style-image"
                    />
                  </div>

                  <!-- 右侧信息区 -->
                  <div class="style-info">
                    <div class="style-item">
                      <div class="style-label">三庭比例:</div>
                      <div class="style-value">
                        {{ styleRef.threePartRatio || "" }}
                      </div>
                    </div>
                    <div class="style-item">
                      <div class="style-label">五眼比例:</div>
                      <div class="style-value">
                        {{ styleRef.fiveEyeRatio || "" }}
                      </div>
                    </div>
                    <div class="style-item">
                      <div class="style-label">风格定位:</div>
                    </div>
                    <div class="style-value">
                      {{ styleRef.stylePositioning || "" }}
                    </div>
                    <div class="style-item">
                      <div class="style-label">妆容重点:</div>
                    </div>
                    <div
                      class="style-value"
                      v-html="formatAdvice(styleRef.makeupFocus)"
                    ></div>
                  </div>
                </div>
              </div>
            </div>

            <div class="section-divider"></div>

            <section class="skin-section html2pic">
              <h2>皮肤分析</h2>
              <div class="skin-analysis">
                <div class="image-container">
                  <img
                    :src="
                      reportData?.skin_analysis.result_image_url ||
                      '../assets/baseDeepPic.png'
                    "
                    alt="肤色分析"
                    class="circle-image"
                  />
                </div>
                <div class="analysis-text">
                  <p>痘痘：{{ reportData?.skin_analysis.acne.result_name }}</p>
                  <p
                    v-html="formatAdvice(reportData?.skin_analysis.acne.advice)"
                  ></p>
                  <p>
                    眼皮：{{
                      reportData?.skin_analysis.eyelid_type.result_name
                    }}
                  </p>
                  <p
                    v-html="
                      formatAdvice(reportData?.skin_analysis.eyelid_type.advice)
                    "
                  ></p>
                  <p>
                    法令纹：{{
                      reportData?.skin_analysis.nasolabial_fold.result_name
                    }}
                  </p>
                  <p
                    v-html="
                      formatAdvice(
                        reportData?.skin_analysis.nasolabial_fold.advice
                      )
                    "
                  ></p>
                  <p>
                    斑点：{{ reportData?.skin_analysis.skin_spot.result_name }}
                  </p>
                  <p
                    v-html="
                      formatAdvice(reportData?.skin_analysis.skin_spot.advice)
                    "
                  ></p>
                  <p>
                    皮肤类型：{{
                      reportData?.skin_analysis.skin_type.result_name
                    }}
                  </p>
                  <p
                    v-html="
                      formatAdvice(reportData?.skin_analysis.skin_type.advice)
                    "
                  ></p>
                </div>
              </div>
            </section>

            <div class="section-divider"></div>

            <section class="proportion-section">
              <h2>比例分析</h2>
              <div class="proportion-analysis html2pic">
                <div class="comparison-images">
                  <div class="comparison-item">
                    <img
                      :src="
                        reportData?.three_part_analysis.result_image_url ||
                        '../assets/baseDeepPic.png'
                      "
                      alt="三庭比例"
                      class="circle-image"
                    />
                    <div class="proportion-label">三庭比例</div>
                    <div class="proportion-data">
                      {{ reportData?.three_part_analysis.ratios }}
                    </div>
                  </div>
                </div>
                <div class="analysis-text proportion-text">
                  <p
                    v-if="reportData?.three_part_analysis.advice"
                    v-html="
                      formatAdvice(reportData?.three_part_analysis.advice)
                    "
                  ></p>
                  <p
                    v-if="
                      reportData?.three_part_analysis.one_part.result !==
                      'faceup_normal'
                    "
                  >
                    上庭：{{
                      reportData?.three_part_analysis.one_part.result_name
                    }}
                    <span
                      v-html="
                        formatAdvice(
                          reportData?.three_part_analysis.one_part.advice
                        )
                      "
                    ></span>
                  </p>
                  <div
                    v-if="
                      reportData?.three_part_analysis.one_part.advice_image_url
                    "
                    class="improvement-steps"
                  >
                    <img
                      :src="
                        reportData?.three_part_analysis.one_part
                          .advice_image_url
                      "
                      alt="上庭建议"
                    />
                  </div>

                  <p
                    v-if="
                      reportData?.three_part_analysis.two_part.result !==
                      'facemid_normal'
                    "
                  >
                    中庭：{{
                      reportData?.three_part_analysis.two_part.result_name
                    }}
                    <span
                      v-html="
                        formatAdvice(
                          reportData?.three_part_analysis.two_part.advice
                        )
                      "
                    ></span>
                  </p>
                  <div
                    v-if="
                      reportData?.three_part_analysis.two_part.advice_image_url
                    "
                    class="improvement-steps"
                  >
                    <img
                      :src="
                        reportData?.three_part_analysis.two_part
                          .advice_image_url
                      "
                      alt="中庭建议"
                    />
                  </div>

                  <p
                    v-if="
                      reportData?.three_part_analysis.three_part.result !==
                      'facedown_normal'
                    "
                  >
                    下庭：{{
                      reportData?.three_part_analysis.three_part.result_name
                    }}
                    <span
                      v-html="
                        formatAdvice(
                          reportData?.three_part_analysis.three_part.advice
                        )
                      "
                    ></span>
                  </p>
                  <div
                    v-if="
                      reportData?.three_part_analysis.three_part
                        .advice_image_url
                    "
                    class="improvement-steps"
                  >
                    <img
                      :src="
                        reportData?.three_part_analysis.three_part
                          .advice_image_url
                      "
                      alt="下庭建议"
                    />
                  </div>
                </div>
                <div
                  class="improvement-steps"
                  v-if="reportData?.three_part_analysis.normal_image_url"
                >
                  <img
                    :src="reportData?.three_part_analysis.normal_image_url"
                  />
                </div>
              </div>
              <div class="section-divider"></div>
              <div class="proportion-analysis html2pic">
                <div class="comparison-images">
                  <div class="comparison-item">
                    <img
                      :src="
                        reportData?.five_eyes_analysis.result_image_url ||
                        '../assets/baseDeepPic.png'
                      "
                      alt="五眼比例"
                      class="circle-image"
                    />
                    <div class="proportion-label">五眼比例</div>
                    <div class="proportion-data">
                      {{ reportData?.five_eyes_analysis.ratios }}
                    </div>
                  </div>
                </div>
                <div class="analysis-text proportion-text">
                  <p>
                    眼距：{{ reportData?.five_eyes_analysis.result_name }}
                    <span
                      v-html="
                        formatAdvice(reportData?.five_eyes_analysis.advice)
                      "
                    ></span>
                  </p>
                </div>
                <div class="improvement-steps">
                  <img
                    :src="
                      reportData?.five_eyes_analysis.image_url ||
                      '../assets/analysisProcess.png'
                    "
                  />
                </div>
              </div>
            </section>

            <div class="section-divider"></div>

            <!-- 五官量感分析 -->
            <div class="feature-container html2pic">
              <h2>五官量感</h2>
              <div class="user-image-container">
                <img
                  :src="
                    reportData?.facial_density.result_image_url ||
                    '../assets/baseDeepPic.png'
                  "
                  alt="用户照片"
                  class="circle-image"
                />
              </div>
              <div class="analysis-content">
                <p class="main-analysis">
                  {{ reportData?.facial_density.result_name }}
                </p>
                <p v-html="formatAdvice(reportData?.facial_density.advice)"></p>
              </div>
            </div>

            <div class="features-list html2pic">
              <h3>五官类型</h3>
              <div class="feature-item">
                <span class="feature-label">眉毛:</span>
                <span class="feature-value">{{
                  reportData?.feature_types?.brows.result_name || ""
                }}</span>
              </div>
              <p
                v-html="formatAdvice(reportData?.feature_types?.brows.advice)"
              ></p>
              <div class="feature-item">
                <span class="feature-label">眼睛:</span>
                <span class="feature-value">{{
                  reportData?.feature_types?.eyes.result_name || ""
                }}</span>
              </div>
              <p
                v-html="formatAdvice(reportData?.feature_types?.eyes.advice)"
              ></p>
              <div class="feature-item">
                <span class="feature-label">鼻子:</span>
                <span class="feature-value">{{
                  reportData?.feature_types?.nose.result_name || ""
                }}</span>
              </div>
              <p
                v-html="formatAdvice(reportData?.feature_types?.nose.advice)"
              ></p>
              <div class="feature-item">
                <span class="feature-label">嘴巴:</span>
                <span class="feature-value">{{
                  reportData?.feature_types?.lip.result_name || ""
                }}</span>
              </div>
              <p
                v-html="formatAdvice(reportData?.feature_types?.lip.advice)"
              ></p>
              <div class="feature-item">
                <span class="feature-label">脸型:</span>
                <span class="feature-value">{{
                  reportData?.feature_types?.face_shape.result_name || ""
                }}</span>
              </div>
              <p
                v-html="
                  formatAdvice(reportData?.feature_types?.face_shape.advice)
                "
              ></p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 新增身材分析报告区域 - 当cateId为33时显示 -->
    <div class="report-content" v-if="cateId == 33">
      <div class="body-analysis">
        <div class="analysis-slider">
          <div class="analysis-slide">
            <!-- 身材类型部分 -->
            <section class="body-type-section html2pic">
              <h2>身材类型</h2>
              <div class="body-type-analysis">
                <div class="image-container">
                  <img
                    :src="
                      reportData?.body_type?.image_url ||
                      '../assets/baseDeepPic.png'
                    "
                    alt="身材类型"
                    class="body-image"
                  />
                </div>
                <div class="body-type-info">
                  <div class="info-item">
                    <span class="info-label">身材类型:</span>
                    <span class="info-value">{{
                      reportData?.body_type?.body_type || ""
                    }}</span>
                  </div>
                  <div class="info-item">
                    <span class="info-label">特性特征:</span>
                    <span
                      class="info-value"
                      v-for="(item, index) in reportData?.body_type?.features"
                      :key="index"
                      >{{ item
                      }}{{
                        index < reportData?.body_type?.features.length - 1
                          ? "、"
                          : ""
                      }}</span
                    >
                  </div>
                </div>
              </div>
            </section>

            <div class="section-divider"></div>

            <!-- 身材比例部分 -->
            <section class="body-proportion-section html2pic">
              <h2>身材比例</h2>
              <div class="body-proportion-analysis">
                <div class="image-container">
                  <img
                    :src="
                      reportData?.body_proportion?.image_url ||
                      '../assets/baseDeepPic.png'
                    "
                    alt="身材比例"
                    class="body-image"
                  />
                </div>
                <div class="proportion-info">
                  <div class="info-item">
                    <span class="info-label">头身比例:</span>
                    <span class="info-value">{{
                      reportData?.body_proportion?.head_to_body || ""
                    }}</span>
                  </div>
                  <div class="info-item">
                    <span class="info-label">头肩比例:</span>
                    <span class="info-value">{{
                      reportData?.body_proportion?.head_to_shoulders || ""
                    }}</span>
                  </div>
                  <div class="info-item">
                    <span class="info-label">上下身比例:</span>
                    <span class="info-value">{{
                      reportData?.body_proportion?.upper_to_lower_body || ""
                    }}</span>
                  </div>
                  <div class="info-item">
                    <span class="info-label">腰臀比例:</span>
                    <span class="info-value">{{
                      reportData?.body_proportion?.waist_to_hip_ratio || ""
                    }}</span>
                  </div>
                </div>
              </div>
            </section>

            <div class="section-divider"></div>

            <!-- 穿搭建议 -->
            <section class="style-suggestion-section html2pic">
              <h2>穿搭建议</h2>
              <div class="style-suggestions-list">
                <div
                  v-for="(suggestion, index) in reportData?.suggestions || []"
                  :key="index"
                  class="suggestion-item"
                >
                  <p v-if="suggestion" v-html="formatAdvice(suggestion)"></p>
                  <div
                    class="suggestion-images"
                    v-if="suggestion.images && suggestion.images.length > 0"
                  >
                    <img
                      v-for="(image, imgIndex) in suggestion.images"
                      :key="imgIndex"
                      :src="image"
                      alt="穿搭建议"
                      class="suggestion-image"
                    />
                  </div>
                </div>
              </div>
            </section>
          </div>
        </div>
      </div>
    </div>

    <!-- 在 template 部分，添加加载指示器 -->
    <div class="loading-overlay" v-if="isLoading">
      <div class="loading-spinner"></div>
      <div class="loading-text">正在生成报告...</div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick } from "vue";
import { useRouter, useRoute } from "vue-router";
import { useI18n } from "vue-i18n";
import { useLanguage } from "../composables/useLanguage";
import html2canvas from "html2canvas";
import { reportApi } from "@/api";

const router = useRouter();
const route = useRoute();
const { t } = useI18n();
const isLoading = ref(false);

const personId = ref("");
const cateId = ref("");
const ID = ref("");
// 添加这段代码来接收参数
const reportData = computed(() => {
  if (route.query.reportData) {
    try {
      return JSON.parse(route.query.reportData);
    } catch (e) {
      console.error("解析报告数据失败:", e);
      return null;
    }
  }
  if (localStorage.getItem("reportData"))
    try {
      return JSON.parse(localStorage.getItem("reportData"));
    } catch (e) {
      console.error("解析报告数据失败:", e);
      return null;
    }
  return null;
});

// 处理advice文本，将句号替换为换行符，如果是数组则每一项换一行
const formatAdvice = (advice) => {
  if (!advice) return "";

  // 如果是数组，将每个元素用<br>连接
  if (Array.isArray(advice)) {
    return advice.join("<br>");
  }

  // 如果是字符串，保持原有逻辑
  return advice.replace(/。/g, "。<br>").replace(/\.$/, ".<br>");
};

// 可以在组件初始化时打印一下数据
if (reportData.value) {
  console.log("接收到的报告数据:", reportData.value);
}

const goBack = () => {
  router.push("/main");
};

const changeRoute = (index) => {
  if (index === 1) {
    // Uploading 选项
    router.push("/main");
  } else if (index === 2) {
    // Report 选项
    router.push("/report");
  } else if (index === 3) {
    // History 选项
    router.push("/history");
  } else if (index === 4) {
    // Change Clothes 选项
    router.push("/change-clothes");
  }
};

const waitImagesLoaded = (container) => {
  const imgs = container.querySelectorAll("img");
  return Promise.all(
    Array.from(imgs).map((img) =>
      img.complete
        ? Promise.resolve()
        : new Promise((resolve) => {
            img.onload = img.onerror = resolve;
          })
    )
  );
};

const generateAndUploadImage = async () => {
  try {
    await nextTick();
    isLoading.value = true;
    const element =
      document.querySelector(".face-analysis") ||
      document.querySelector(".body-analysis");
    if (!element) throw new Error("找不到报告内容");

    // 1. 记录原始样式
    const originalOverflow = element.style.overflow;
    const originalPosition = element.style.position;

    // 2. 展开所有内容
    element.style.overflow = "visible";
    element.style.position = "static";
    element.scrollTop = 0;

    // 3. 等待图片加载
    await waitImagesLoaded(element);
    await new Promise((resolve) => setTimeout(resolve, 500));

    // 4. 截图
    const canvas = await html2canvas(element, {
      scale: 2,
      dpi: 400,
      useCORS: true,
      allowTaint: true,
      backgroundColor: "rgba(128, 128, 128, 0.5)",
      logging: false,
    });
    const imageData = canvas.toDataURL("image/png");
    const blob = await (await fetch(imageData)).blob();

    // 5. 恢复原样式
    element.style.overflow = originalOverflow;
    element.style.position = originalPosition;

    // 6. 下载图片
    // const link = document.createElement("a");
    // link.href = imageData;
    // link.download = "report.png";
    // document.body.appendChild(link);
    // link.click();
    // document.body.removeChild(link);

    // 7. 上传
    const formData = new FormData();
    formData.append("files", blob, "report.png");
    formData.append("personId", personId.value);
    formData.append("cateId", cateId.value);
    formData.append("id", ID.value);
    isLoading.value = false;
    const response = await reportApi.createRecord(formData);
    console.log("图片上传成功:", response.msg);
  } catch (err) {
    isLoading.value = false;
    console.error("图片生成或上传失败:", err);
  }
};

// 修改 onMounted
onMounted(async () => {
  if (route.query.personId) {
    personId.value = route.query.personId;
  }
  cateId.value = route.query.cateId || localStorage.getItem("cateId");
  ID.value = route.query.ID;
  // 等待报告数据加载完成
  if (reportData.value) {
    // 给页面一个加载时间，确保所有图片都加载完成
    setTimeout(async () => {
      // 检查是否从 ImageEditor 进入（通过 URL 参数或 localStorage）
      const fromImageEditor =
        localStorage.getItem("fromImageEditor") === "true";

      if (fromImageEditor) {
        await generateAndUploadImage();
        // 清除标志，防止刷新页面时再次生成
        localStorage.removeItem("fromImageEditor");
      }
    }, 3000);
  }
});
</script>

<style lang="less" scoped>
.report {
  width: 100%;
  height: 100vh;
  background-position: center center;
  background-image: url(/src/assets/baseDeepPic.png);
  background-size: cover;
  background-repeat: no-repeat;
  color: white;
  overflow-y: auto;

  .header {
    padding: 20px 0;
    height: 30px;

    .back {
      position: absolute;
      left: 20px;
      top: 20px;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      background: rgba(255, 255, 255, 0.2);
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      span {
        background: url(../assets/icon/back_icon.png) no-repeat center center;
        width: 12px;
        height: 21px;
      }
    }
  }

  .navigation {
    display: flex;
    justify-content: center;
    gap: 40px;
    height: 30px;
    padding: 0 10px;
    overflow-x: auto;
    white-space: nowrap;
    scrollbar-width: none;

    &::-webkit-scrollbar {
      display: none;
    }

    div {
      cursor: pointer;
      opacity: 0.7;
      flex-shrink: 0;

      &.active {
        opacity: 1;
        position: relative;
        font-weight: 500;

        &:after {
          content: "";
          position: absolute;
          bottom: -5px;
          left: 0;
          right: 0;
          height: 2px;
          background-color: white;
        }
      }
    }
  }

  .report-content {
    display: flex;
    justify-content: center;
    padding: 0 20px;
    font-size: 12px;
    .face-analysis {
      max-width: 600px;
      background: rgba(128, 128, 128, 0.5);
      border-radius: 20px;
      padding: 0;
      backdrop-filter: blur(10px);
      position: absolute;
      overflow-y: auto;
      bottom: 1rem;
      top: 8rem;
      left: 2rem;
      right: 2rem;

      .analysis-slider {
        height: 100%;

        .analysis-slide {
          width: 100%;
          padding: 10px 20px;
          box-sizing: border-box;

          h2 {
            text-align: center;
            margin-bottom: 20px;
            font-weight: normal;
          }

          // 五官量感分析
          .feature-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-bottom: 20px;
          }

          .user-image-container {
            margin-bottom: 15px;
          }

          .circle-image {
            width: 120px;
            height: 120px;
            border-radius: 50%;
            object-fit: cover;
            margin-bottom: 8px;
            border: 2px solid white;
          }

          .analysis-content {
            text-align: left;
            width: 100%;
          }

          .main-analysis {
            font-weight: bold;
            margin-bottom: 10px;
          }

          .analysis-content ol {
            padding-left: 20px;
            margin-top: 10px;
          }

          .analysis-content li {
            margin-bottom: 5px;
          }

          h3 {
            text-align: left;
            margin: 20px 0 10px 0;
            font-weight: normal;
          }

          .features-list {
            width: 100%;
            text-align: left;
          }

          .feature-item {
            margin-bottom: 10px;
            display: flex;
            align-items: baseline;
          }

          .feature-label {
            font-weight: bold;
            min-width: 60px;
          }

          .feature-value {
            flex: 1;
          }

          // 第二个分析页面中的各部分
          section {
            margin-bottom: 25px;
          }

          .section-divider {
            height: 1px;
            background-color: rgba(255, 255, 255, 0.3);
            margin: 25px 0;
          }

          // 肤色分析
          .skin-analysis {
            display: flex;
            flex-direction: column;
            align-items: start;

            .image-container {
              position: relative;
              margin-bottom: 15px;
              display: flex;
              justify-content: center;
              width: 100%;
              .circle-image {
                width: 120px;
                height: 120px;
                border-radius: 50%;
                object-fit: cover;
                margin-bottom: 8px;
                border: 2px solid white;
              }

              .color-circle {
                position: absolute;
                width: 50px;
                height: 50px;
                border-radius: 50%;
                background-color: #f5e6e0; // 肤色圆点
                top: 0;
                right: -25px;
                border: 2px solid white;
              }
            }
          }

          // 比例分析
          .proportion-analysis {
            .comparison-images {
              display: flex;
              justify-content: center;
              margin-bottom: 15px;

              .comparison-item {
                text-align: center;

                .circle-image {
                  width: 120px;
                  height: 120px;
                  border-radius: 50%;
                  object-fit: cover;
                  margin-bottom: 8px;
                  border: 2px solid white;
                }

                .proportion-label {
                  margin-bottom: 5px;
                }

                .proportion-data {
                  opacity: 0.8;
                }
              }
            }

            .proportion-text {
              margin-bottom: 15px;
            }

            .improvement-steps {
              display: flex;
              justify-content: space-between;
              margin-top: 15px;
              img {
                width: 100%;
                height: 100%;
              }
              .step-image {
                width: 32%;
                height: auto;
                object-fit: cover;
                border-radius: 5px;
              }
            }
          }

          // 面部留白
          .face-blank-analysis {
            display: flex;
            align-items: center;
            justify-content: center;

            .image-container {
              .circle-image {
                width: 120px;
                height: 120px;
                border-radius: 50%;
                object-fit: cover;
                margin-bottom: 8px;
                border: 2px solid white;
              }
            }
          }

          .analysis-text {
            line-height: 1.6;
            text-align: left;
            p {
              margin: 4px 0;
            }
          }

          // 风格参考样式
          .style-reference {
            margin-top: 15px;
          }

          .style-card {
            border-radius: 15px;
            margin-bottom: 20px;
          }

          .style-content {
            display: flex;
            align-items: flex-start;
            gap: 10px;
            overflow: hidden;
          }

          .style-image-section {
            border-radius: 15px;
            overflow: hidden;
          }

          .style-image {
            width: 100%;
            border-radius: 15px 15px 0 0;
            display: block;
          }

          .color-palette {
            display: flex;
            justify-content: space-between;
            background: rgba(255, 255, 255, 0.9);
            padding: 10px;
            border-radius: 0 0 15px 15px;
          }

          .color-chip {
            width: 18%;
            height: 30px;
            border-radius: 5px;
          }

          .style-info {
            flex: 1;
            text-align: left;
          }

          .style-item {
            display: flex;
            margin-top: 10px;
            align-items: baseline;
          }

          .style-label {
            font-weight: bold;
            min-width: 65px;
            text-align: left;
          }

          .style-value {
            flex: 1;
            text-align: left;
          }
        }
      }
    }
  }
}

// 添加滚动条样式
::-webkit-scrollbar {
  width: 4px;
}

::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 2px;
}

::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 2px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.5);
}

/* 响应式适配 */
@media (max-width: 580px) {
  .style-content {
    flex-direction: column;
  }

  .style-image-section {
    max-width: 100%;
    margin-bottom: 15px;
  }
}

/* 身材分析样式 */
.body-analysis {
  max-width: 600px;
  background: rgba(128, 128, 128, 0.5);
  border-radius: 20px;
  padding: 0;
  backdrop-filter: blur(10px);
  position: absolute;
  overflow-y: auto;
  bottom: 1rem;
  top: 8rem;
  left: 2rem;
  right: 2rem;

  .analysis-slider {
    height: 100%;

    .analysis-slide {
      width: 100%;
      padding: 10px 20px;
      box-sizing: border-box;

      h2 {
        text-align: center;
        margin-bottom: 20px;
        font-weight: normal;
      }
    }
  }
}

.body-type-analysis,
.body-proportion-analysis {
  display: flex;
  flex-direction: column;
  align-items: center;

  .image-container {
    margin-bottom: 20px;
    text-align: center;

    .body-image {
      max-width: 160px;
      max-height: 240px;
      object-fit: contain;
      border: 2px solid white;
      border-radius: 10px;
    }
  }
}

.body-type-info,
.proportion-info {
  width: 100%;

  .info-item {
    display: flex;
    margin-bottom: 10px;
    align-items: baseline;
    text-align: left;
  }

  .info-label {
    font-weight: bold;
    min-width: 80px;
  }
}

.style-suggestions-list {
  text-align: left;

  .suggestion-item {
    margin-bottom: 25px;
    padding-bottom: 15px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);

    &:last-child {
      border-bottom: none;
    }

    h3 {
      font-size: 14px;
      margin-bottom: 10px;
      font-weight: bold;
    }

    p {
      margin-bottom: 15px;
      line-height: 1.6;
    }
  }

  .suggestion-images {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 10px;

    .suggestion-image {
      width: calc(50% - 5px);
      border-radius: 8px;
      object-fit: cover;
    }
  }
}

/* 在 style 部分添加加载样式 */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 4px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 1s ease-in-out infinite;
  margin-bottom: 20px;
}

.loading-text {
  color: white;
  font-size: 18px;
}
</style>
