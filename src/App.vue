<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-md-8">
          <div class="form-group">
            <label for="text-input">Enter your text:</label>
            <b-form-textarea
              v-model="inputText"
              id="text-input"
              rows="10"
              class="form-control"
            ></b-form-textarea>
          </div>
          <div class="text-right mt-2">
            <b-button variant="primary" @click="summarizeText"
              >Summarize</b-button
            >
          </div>
        </div>
        <div class="col-md-4">
          <b-card>
            <template #header>Summary</template>
            <b-card-text>
              <p v-if="!summary">
                Enter your text and click Summarize to see the summary.
              </p>
              <p v-if="summary">{{ summary.join(" ") }}</p>
            </b-card-text>
            <b-button-group class="mb-2">
              <b-button variant="secondary" @click="downloadSummary"
                >Download Summary</b-button
              >
            </b-button-group>
          </b-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputText: "",
      summary: "",
      apiKey: "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",
      model: "text-davinci-002",
      temperature: 0.5,
      maxTokens: 60,
      topP: 1,
    };
  },
  methods: {
    async summarizeText() {
      let headers = new Headers({
        "Content-Type": "application/json",
        Authorization: `Bearer ${this.apiKey}`,
      });

      let prompt = `Please summarize the following text: "${this.inputText}"`;
      let body = JSON.stringify({
        model: this.model,
        prompt,
        temperature: this.temperature,
        max_tokens: this.maxTokens,
        top_p: this.topP,
      });

      let requestOptions = {
        method: "POST",
        headers,
        body,
      };

      try {
        const response = await fetch(
          "https://api.openai.com/v1/completions",
          requestOptions
        );
        let data = await response.json();
        let summary = data.choices[0].text.split(". ");
        this.summary = summary;
      } catch (error) {
        console.log(error);
      }
    },
    downloadSummary() {
      if (!this.summary) {
        return;
      }

      const dataUrl =
        "data:text/plain;charset=utf-8," +
        encodeURIComponent(this.summary.join("\n"));
      const link = document.createElement("a");
      link.href = dataUrl;
      link.download = "summary.txt";
      link.click();
    },
  },
};
</script>
<style>
body {
  font-family: Arial, sans-serif;
  min-height: 100vh;
  background-color: #f8f8f8 !important;
  padding-top: 50px;
}

.container {
  margin: 0 auto;
}

.card {
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  padding: 16px;
  margin-bottom: 32px;
}

.card-header {
  background-color: #007bff;
  color: #fff;
  font-size: 18px;
  font-weight: bold;
}

.card-text {
  font-size: 16px;
  line-height: 1.5;
}

label {
  font-weight: bold;
  font-size: 18px;
}

.form-control {
  font-size: 16px;
  height: auto;
}

.btn {
  font-size: 16px;
  padding: 8px 16px;
}

.btn-secondary {
  font-size: 16px;
  padding: 8px 16px;
}
</style>


