<template>
  <main class="main-container">
    <MemberOption class="sidebar"></MemberOption>
    <div class="content-container">
      <div class="profile-card">
        <div class="profile-header">
          <h1 class="brand-title">APPLE TREE</h1>
          <h6 class="display-4 fw-normal">更改評論</h6>
          <label>訂單編號：{{ feedback.orderID }}</label>
        </div>
        <div class="horizontal-divider"></div>

                <div class="container">
                  <div v-if="feedback">
                    <div>
                      <label for="feedbackType">類型：</label>
                      <select v-model="feedback.type" id="feedbackType" required>
                        <option value="服務態度">服務態度</option>
                        <option value="商品品質">商品品質</option>
                        <option value="配送速度">配送速度</option>
                        <option value="售後服務">售後服務</option>
                        <option value="其它">其它</option>
                      </select>
                    </div>
                    <p/>
                    <div class="form-group">
                      <label for="feedbackDescription" class="form-label">內容：</label>
                      <textarea v-model="feedback.description" id="feedbackDescription" class="textarea-large" required>配送速度快速，非常滿意</textarea>
                    </div>
                    <button @click="submitFeedback" class="myButton">提交更新</button>
                  </div>
                </div>

            </div>
    </div>
  </main>

  <div class="modal fade" id="blockedAccountModalUp" tabindex="-1" aria-labelledby="blockedAccountModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header bg-light text-black">
          <h5 class="modal-title" id="blockedAccountModalLabel">提示</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          意見反饋更新成功
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MemberOption from "@/components/MemberOption.vue";
import { useUserStore } from "@/stores/userStore"; //user store
import axios from "axios";

// CustomerFeedbackUpdate.vue
import { useFeedbackStore } from '@/stores/feedbackStore';

export default {
  data() {
    return {
      isLoggedInUserId: null,
      feedback: useFeedbackStore().feedback,
      showSuccessModal: false  // 控制模态窗口的显示
    };
  },
  created() {
    const feedbackStore = useFeedbackStore();
    this.feedback = feedbackStore.feedback;
    if(!feedbackStore.feedback){
      this.$router.push("/MemberCenter/MemberFeedback");
    }
  },
  unmounted() {
    const feedbackStore = useFeedbackStore();
    feedbackStore.clearFeedback();
  },
  components: {
    MemberOption,
  },
  methods: {
    closeSuccessModal() {
      this.showSuccessModal = false;  // 隐藏模态窗口
    },

    submitFeedback() {
      axios.put(`${this.API_URL}/update/customerFeedbacks`, {
        ...this.feedback,
        feedbackDate: new Date().toISOString()
      })
          .then(response => {
            this.showSuccessModal = true;
            var myModal = new bootstrap.Modal(document.getElementById('blockedAccountModalUp'));
            myModal.show();
            this.$router.push('/MemberCenter/MemberFeedback');
          })
          .catch(error => {
            alert('評論更新失敗！');
            console.error(error);
          });

    },
  },
  mounted() {
    const userStore = useUserStore();
    if (userStore.isLoggedIn) {
      this.isLoggedInUserId = userStore.userId;
    } else {
      console.log("會員未登入");
    }
  },
  computed: {

  },
};
</script>
<style scoped>
.form-group {
  display: flex;
  align-items: flex-start;
  margin-bottom: 10px;
}

.form-label {
  margin-right: 10px;
  white-space: nowrap;
}

.textarea-large {
  flex-grow: 1;
  min-height: 150px;
  padding: 10px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
  resize: vertical;
  margin-top: 0;
}

.main-container {
  display: flex;
  min-height: 100vh;

}
.sidebar {
  width: 250px;
  background-color: #333;
  padding: 20px;
  color: white;
  display: flex;
  flex-direction: column;
}

.content-container {
  flex-grow: 1;
  padding: 20px;
  background-color: #f8f9fa;
  display: flex;
  justify-content: center;
  align-items: center;
}

.profile-card {
  width: 100%;
  max-width: 1000px;
  padding: 20px;
  border-radius: 6px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.profile-header {
  text-align: center;
  margin-bottom: 30px;
}

.brand-title {
  font-size: 2.5em;
  color: #333;
}

.brand-slogan {
  font-size: 1em;
  color: #666;
}

.horizontal-divider {
  width: 100%;
  height: 1px;
  background-color: #ccc;
  margin-bottom: 20px;
}

.myButton {
  padding: 10px 15px;
  border: black solid 3px;
  border-radius: 25px;
  background-color: white  !important;
  color: 	black  !important;
  cursor: pointer;
  font-weight: bold;
  text-transform: uppercase;
  display: block;
  margin: auto;
}

.myButton:hover {
  background-color:		black !important; /* For example, a green button */
  color: white !important;
}

</style>
