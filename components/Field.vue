<template>
    <section class="content">
      <div class="pannel">
        <div class="pannel__item">
          <!-- <p class="text">Field border type</p> -->
          <label class="control-wrap">
            <input v-model="activeBorders" type="checkbox" name="fieledBorder" class="checkbox" />
            <p class="control-text">turn off borders</p>
          </label>

          <div class="speed-wrap">
            <p class="control-text">life cycle speed: {{ speed  }} ms</p>
            <div>
              <button @click="speed = speed + 100">slower</button>
              <button @click="speed = speed - 100">faster</button>
            </div>
          </div>
        </div>
        <button class="button" @click="handleSimulation">{{ simulationStarted ? 'pause' : 'start simulation'}}</button>
      </div>
      <div>

        <div class="main-field" :class="{ 'main-field_type_infinite': activeBorders }">
          <div v-for="row in world.rows" :key="row.id"  class="row">
            <div v-for="cell in row.cells" :key="cell.id" class="cell" 
              :class="{alive: cell.isAlive}" 
              @click="handleLife(row, cell)">
            </div>
          </div>
        </div>

      </div>
    </section>
</template>

<script>


export default {
  name: 'Field',

  
  data() {
    return {
      simulationStarted: false,
      speed: 600,
      mount: 50,
      activeBorders: true,
      world: {
        rows: [],
      },
      activeCell: false,
    }
  },

  mounted() {
    const totalCounter = this.mount;
    let i = 0;

    function generateCells() {
      let j = 0;
      const cells = [];
      while(j < totalCounter) {
        cells.push({ id: j, isAlive: false });
        j++;
      }
      return { id: i, cells,}      
    }

    while(i < totalCounter) {
      this.world.rows.push(generateCells());
      i++;
    }

    console.log(this.world)
  },

  methods: {
    handleLife(row, cell) {
      cell.isAlive = !cell.isAlive;
      this.activeCell = {row: row.id, cell: cell.id}
    },

    checkCellStatus(rowId, cellId) {

      let aliveCounter = 0;

      let rowCounter = -1;
      while (rowCounter <= 1) {

        if(this.world.rows[rowId + rowCounter]) {
          let cellCounter = -1;
          while(cellCounter <= 1) {

            if(this.world.rows[rowId + rowCounter].cells[cellId + cellCounter]) {
              if(this.world.rows[rowId + rowCounter].cells[cellId + cellCounter].isAlive) {
                if ( rowCounter === 0 && cellCounter === 0) {
                  aliveCounter--;
                }
                aliveCounter++;
              }
            }
            cellCounter++;
          }
        }
        rowCounter++;
      }

      // console.log('aliveCounter', aliveCounter)

      // debugger;
      if(aliveCounter <= 1 && this.world.rows[rowId].cells[cellId].isAlive) {
        setTimeout(() => {this.world.rows[rowId].cells[cellId].isAlive = false}, 0) ;
      } else if (aliveCounter >= 4 && this.world.rows[rowId].cells[cellId].isAlive) {
        setTimeout(() => {this.world.rows[rowId].cells[cellId].isAlive = false}, 0);
      } else if (aliveCounter === 3 && !this.world.rows[rowId].cells[cellId].isAlive) {
        setTimeout(() => {this.world.rows[rowId].cells[cellId].isAlive = true}, 0);
      }
      
    },

    handleSimulation() {
      if (this.simulationStarted) { 
        clearInterval(this.simulationStarted);
        this.simulationStarted = false;
      } else {
        this.simulationStarted  = setInterval(this.makeSimulation, this.speed);
      }

      
      
      // setTimeout(() => { clearInterval(simulation) }, 10000);
    },
    
    makeSimulation() {
      this.world.rows.forEach((row, rowIndex) => {
        row.cells.forEach(cell => {
          // if(cell.isAlive) {
          //   console.log(`clicked cell: ${row.id}-${cell.id}`)
          // }

          this.checkCellStatus(row.id, cell.id)

        })
      })
    }
  },
}

</script>

<style scoped>

.content {
  margin: 0 auto;
  max-width: fit-content;
  display: flex;
  align-items: flex-start;
  column-gap: 20px;
  max-width: min-content;
  
}

.pannel {
  
  display: flex;
  flex-direction: column;
  min-width: 300px;
}

.pannel__item {
  
}

.button {
  margin: 0;
  padding: 5px 10px;
}

.text {
  padding: 0;
  margin: 0;
}

.main-field {
  /* width: 500px; */
  border: 1px solid rgb(186 157 157 / 87%);
  box-shadow: 3px 2px 4px 0px #ba9d9dde;
  display: flex;
  flex-direction: column;
  max-width: fit-content;
  margin: 0 auto;
  transition: box-shadow .4s ease, border .4s ease;
}

.main-field_type_infinite {
  border: 1px solid rgb(186 157 157 / 87%);
  box-shadow: 0px 0px 4px 2px rgb(238 114 114 / 87%);
}



.row {
  display: flex;
}

.cell {
  border: 1px solid rgb(209 209 209 / 71%);
  width: 10px;
  height: 10px;
  transition: background-color .4s ease;
}

.alive {
  background-color: #528ea9;
  
}

.text {
  margin: 0;
  padding: 0;
}

.pannel {
  border: 1px solid rgb(209 209 209 / 71%);
  padding: 10px;
  box-sizing: border-box;
  display: flex;
  row-gap: 40px;
}

.pannel__item {

  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  row-gap: 20px;

}

.control-wrap {
  display: flex;
  align-items: center;
  margin: 0;
  padding: 0;
}



.control-text {
  padding: 0;
  margin: 0;
}

.checkbox:checked + .checkbox-visual::before {
  left: 20px;
}


.speed-wrap {
  display: flex;
  flex-direction: column;
  row-gap: 10px;
}


</style>
