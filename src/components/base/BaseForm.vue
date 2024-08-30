<template>
  <div class="form-container">
    <form @submit.prevent="handleSubmit">
      <div class="input-container">
        <input
          type="text"
          id="text-input"
          v-model="textInput"
          class="input"
          placeholder="Введите текст"
        />
        <div v-if="textError" class="error-message">{{ textError }}</div>
      </div>

      <div class="input-container">
        <input
          type="number"
          id="otp-input"
          v-model="otpInput"
          class="input"
          placeholder="Введите код"
        />
        <div v-if="otpError" class="error-message">{{ otpError }}</div>
      </div>

      <div class="input-container">
        <div class="file-input-container" @dragover.prevent @drop="handleDrop">
          <input
            type="file"
            id="file-input"
            @change="handleFileChange"
            class="file-input"
            multiple
          />
          <div class="file-input-text"  v-if="files.length">
            Файлы (файл) загружены
          </div>
          <div class="file-input-text" v-else>
            Загрузите файлы (файл) (png jpeg jpg xlsx csv)
          </div>
        </div>
        <div v-if="fileError" class="error-message">{{ fileError }}</div>
      </div>

      <button type="submit" class="submit-button">Отправить</button>
    </form>
  </div>
</template>

<script>
export default {
  name:'BaseForm',
  data() {
    return {
      textInput: "",
      otpInput: "",
      files: [],
      textError: "",
      otpError: "",
      fileError: "",
    };
  },
  methods: {
    handleSubmit() {
      if (this.validateForm()) {
        console.log("Текст:", this.textInput);
        console.log("Код:", this.otpInput);
        console.log("Файлы:", this.files);
        this.$router.push({
          path: "/about",
          query: {
            textInput: this.textInput,
            otpInput: this.otpInput,
            files: JSON.stringify(
              this.files.map((file) => ({ name: file.name }))
            ),
          },
        });
      }
    },
    handleFileChange(event) {
      this.files = Array.from(event.target.files);
    },
    handleDrop(event) {
      event.preventDefault();
      this.files = Array.from(event.dataTransfer.files);
    },
    validateForm() {
      let isValid = true;

      if (!this.textInput) {
        this.textError = "Пропущен текст";
        isValid = false;
      } else {
        this.textError = "";
      }

      if (!this.otpInput || /^(.)\1{3}$/.test(this.otpInput)) {
        this.otpError = "Пропущен код или некорректный формат";
        isValid = false;
      } else {
        this.otpError = "";
      }

      if (this.files.length === 0) {
        this.fileError = "Файл не выбран";
        isValid = false;
      } else {
        const validExtensions = ["jpg", "jpeg", "png", "xlsx", "csv"];
        const maxSize = 5 * 1024 * 1024; // 5MB
        
        const invalidFiles = this.files.filter((file) => {
          console.log(file);
          const extension = file.name.split(".").pop().toLowerCase();
          return !validExtensions.includes(extension) || file.size > maxSize;
        });

        if (invalidFiles.length > 0) {
          this.fileError = "Файл с ошибкой или неверный формат";
          isValid = false;
        } else {
          this.fileError = "";
        }
      }

      return isValid;
    },
  },
};
</script>

<style lang="less" scoped>
@primary-color: #3498db;
@error-color: #e74c3c;
@border-radius: 4px;
@padding: 10px;
@font-size: 16px;

.form-container {
  width: 100%;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: @border-radius;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.input-container {
  margin-bottom: 20px;

  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }

  .input {
    width: 100%;
    padding: @padding;
    font-size: @font-size;
    border: 1px solid #ccc;
    border-radius: @border-radius;
    box-sizing: border-box;
    transition: border-color 0.3s ease;

    &:focus {
      border-color: @primary-color;
      outline: none;
    }

    &::placeholder {
      color: #aaa;
    }
  }

  .error-message {
    color: @error-color;
    font-size: 12px;
    margin-top: 5px;
  }
}

.file-input-container {
  position: relative;
  width: 100%;
  padding: @padding;
  border: 1px dashed #ccc;
  border-radius: @border-radius;
  text-align: center;
  cursor: pointer;

  .file-input {
    position: absolute;
    width: 100%;
    height: 100%;
    opacity: 0;
    cursor: pointer;
  }

  .file-input-text {
    font-size: @font-size;
    color: #aaa;
  }
}

.submit-button {
  display: block;
  width: 100%;
  padding: @padding;
  font-size: @font-size;
  background-color: @primary-color;
  color: #fff;
  border: none;
  border-radius: @border-radius;
  cursor: pointer;
  transition: background-color 0.3s ease;

  &:hover {
    background-color: darken(@primary-color, 10%);
  }
}
</style>
