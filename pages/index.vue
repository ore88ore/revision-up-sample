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
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage',
  data: () => {
    return {
      serverRevision: '',
    }
  },
  computed: {
    appRevisionId(): string {
      return process.env.APP_REVISION_ID ?? ''
    },
  },
  async asyncData({ app }) {
    const res = await fetch(
      `${process.env.REVISION_URL}/application/revision.json`
    )
    const revision = await res.json()
    return {
      serverRevision: revision.revision ?? '',
    }
  },
})
</script>
