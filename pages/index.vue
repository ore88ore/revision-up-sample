<template>
  <div>
    <h2>RevisionUp テストです</h2>
    <p>
      アプリケーションが保持しているリビジョンID:
      <span style="color: blue">{{ appRevisionId }}</span>
    </p>
    <p>
      CloudFront + S3 から取得したリビジョンID:
      <span style="color: blue">{{ serverRevision }}</span>
    </p>
    <p>
      リビジョンIDを比較:
      <span v-if="appRevisionId === serverRevision" style="color: blue"
        >同じです</span
      ><span v-else style="color: red">異なります！！</span>
    </p>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
  data(): any {
    return {
      serverRevision: '',
      intervalId: 0,
    }
  },
  computed: {
    appRevisionId(): string {
      return process.env.APP_REVISION_ID ?? ''
    },
  },
  methods: {
    async getRevisionId(): Promise<void> {
      try {
        const res = await fetch(
          `${process.env.REVISION_URL}/application/revision.json`
        )
        const revision = await res.json()
        this.serverRevision = revision?.revision ?? ''
      } catch (error) {
        console.error(error)
      }
    },
  },
  mounted() {
    this.getRevisionId()
    this.intervalId = window.setInterval(() => {
      this.getRevisionId()
    }, 5 * 1000) // 5秒に1回ポーリング
  },
  beforeDestroy() {
    window.clearInterval(this.intervalId)
  },
})
</script>
