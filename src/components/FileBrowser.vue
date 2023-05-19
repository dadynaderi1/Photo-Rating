<script>
import fs from 'fs'
import pathModule from 'path'
import { app } from '@electron/remote'
import { computed, ref } from 'vue'
import emitter from '@/eventBus'

import FilesViewer from './FilesViewer'
const formatSize = size => {
  var i = Math.floor(Math.log(size) / Math.log(1024))
  return (
    (size / Math.pow(1024, i)).toFixed(2) * 1 +
    ' ' +
    ['B', 'kB', 'MB', 'GB', 'TB'][i]
  )
}

export default {
  name: 'FileBrowser',
  components: { FilesViewer },
  setup() {

    const path = ref(app.getAppPath())
    emitter.emit('select-folder',this,path)
    const files = computed(() => {
      const fileNames = fs.readdirSync(path.value)
      return fileNames
        .map(file => {
          const stats = fs.statSync(pathModule.join(path.value, file))
          return {
            name: file,
            size: stats.isFile() ? formatSize(stats.size ?? 0) : null,
            directory: stats.isDirectory()
          }
        })
        .sort((a, b) => {
          if (a.directory === b.directory) {
            return a.name.localeCompare(b.name)
          }
          return a.directory ? -1 : 1
        })
    })

    const back = () => {
      path.value = pathModule.dirname(path.value)
    }
    const open = folder => {
      path.value = pathModule.join(path.value, folder)
    }

    const searchString = ref('')
    
    const filteredFiles = computed(() => {
      return searchString.value
        ? files.value.filter(s => s.name.startsWith(searchString.value))
        : files.value
        
    })
    
    return {
      path,

      open,
      back,

      files,
      searchString,
      filteredFiles
    }
  }
}


</script>

<template>
  <!-- Sidebar -->
  <nav id="sidebar">
      <div class="sidebar-header">
          <h5 @change="select-folder">{{ path }}</h5>
      </div>

      <ul class="list-unstyled components">
            <FilesViewer
      :files="filteredFiles"
      :nested="nested"
      @back="back"
      @folderclick="open($event.name)"
    />
      </ul>
  </nav>
</template>

<style scoped>
@import "https://fonts.googleapis.com/css?family=Poppins:300,400,500,600,700";
ul ul a {
    font-size: 0.9em !important;
    padding-left: 30px !important;
}
#sidebar {
  min-width: 300px;
max-width: 300px;
min-height: 100vh;
background: #1d1919;
color: #fff;
}

</style>