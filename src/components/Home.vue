/* eslint-disable standard/object-curly-even-spacing */
/* eslint-disable standard/object-curly-even-spacing */
<template>
  <div class="home">
    <h1>Welcome to robot walk programming</h1>
    <v-form>
      <v-container>
        <v-row>
          <v-col
            cols="12"
            sm="6"
            md="3">
            <v-text-field
              v-model="roomSize"
              class="centered-input text--darken-3 mt-3"
              color="#330088"
              type="string"
              label="Enter a room size e.g 5x5 etc"
            />
          </v-col>
          <v-col
            cols="12"
            sm="6"
            md="3">
            <v-text-field
              v-model="robotStartLocation"
              class="centered-input text--darken-3 mt-3"
              color="#330088"
              type="string"
              label="Enter robot location e.g 1,2"
            />
          </v-col>
          <v-col
            cols="12"
            sm="6"
            md="3"
            d-flex>
            <v-select
              :items="robotDirections"
              v-model="robotStartDirection"
              class="centered-input text--darken-3 mt-3"
              color="#330088"
              label="Select robot direction"
            />
          </v-col>
          <v-col
            cols="12"
            sm="6"
            md="3">
            <v-text-field
              v-model="robotCommandString"
              class="centered-input  mt-3"
              color="#330088"
              text
              type="string"
              label="Enter commands e.g RFLFFLRF"
            />
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <div class="text-center">
      <v-btn
        rounded
        color="#330088"
        dark
        @click="checkResults()">
        Click results
      </v-btn>
      <v-container>
        <v-row
          justify="center">
          <v-col
            cols="12"
            sm="6"
            md="3"
          >
            <p>Results:</p>
            <v-text-field
              v-model="robotEndPosition"
              class="centered-input text--darken-3 mt-3"
              color="#330088"
              label="Robot position after walk"
              filled
              readonly
            />
          </v-col>
        </v-row>
      </v-container>
    </div>
  </div>
</template>
<script>
export default {
    name: "Home",

    data() {
        return {
            maxX: 0,
            maxY: 0,
            roomSize: "5x5",
            robotPos: { curDir: "", curX: 0, curY: 0 },
            robotStartLocation: "1,2",
            robotStartDirection: "North",
            robotDirections: ["North", "East", "South", "West"],
            robotCommandString: "RFRFFRFRF",
            robotCommands: [],
            robotEndPosition: ''
        };
    },
    methods: {
        // parse room size
        parseRoomSize() {
            let roomLimits = this.roomSize.split("x")
            this.maxX = parseInt(roomLimits[0])
            this.maxY = parseInt(roomLimits[1])
            if (isNaN(this.maxX) || isNaN(this.maxY)) {
                alert('could not parse room size')
                return false
            }
            if (this.maxX < 2 || this.maxY < 2) {
                alert('room size must be at least 2x2')
                return false
            }
            return true
        },

        //parse the robot location
        parseRobotLocation() {
            let robotLocation = this.robotStartLocation.split(",")
            this.robotPos.curX = parseInt(robotLocation[0])
            this.robotPos.curY = parseInt(robotLocation[1])
            if (isNaN(this.robotPos.curX) || isNaN(this.robotPos.curY)) {
                alert('could not parse robot location')
                return false
            }
            if (this.robotPos.curX < 0 || this.robotPos.curY < 0) {
                alert('robot location x,y must be greater than 0')
                return false
            }
            if (this.robotPos.curX >= this.maxX || this.robotPos.curY >= this.maxY) {
                alert('robot location x,y is bigger than room size')
                return false
            }
            return true

        },

        // verify each direction
        verifyDirection() {
            if (!this.robotDirections.includes(this.robotStartDirection)) {
                alert('direction has to be North/East/South/West')
                return false
            }
            this.robotPos.curDir = this.robotStartDirection
            return true
        },

        // verify commands
        verifyCommands() {
            let commands = this.robotCommandString.split("")
            for (let command of commands) {
                if (command !== 'L' && command !== 'R' && command !== 'F') {
                    alert('commands can only be L/R/F')
                    return false
                }
            }
            this.robotCommands = commands
            return true
        },

        // run commands
        runCommands() {
            for (let command of this.robotCommands) {
                // console.log(command)
                if (command === 'R') {
                    this.turnRight(this.robotPos)
                } else if (command === 'L') {
                    this.turnLeft(this.robotPos)
                } else if (command === 'F') {
                    this.walkForward(this.robotPos)
                } else {
                    throw console.error();
                }
            }
        },

        // check reseults from each function
        checkResults() {
            if (this.parseRoomSize() === false) {
                return
            }
            if (this.parseRobotLocation() === false) {
                return
            }
            if (this.verifyDirection() === false) {
                return
            }
            if (this.verifyCommands() === false) {
                return
            }
            this.runCommands()
            console.log(`Result: X ${this.robotPos.curX} Y ${this.robotPos.curY} DIR ${this.robotPos.curDir}`)
            this.robotEndPosition = `${this.robotPos.curX}, ${this.robotPos.curY} ${this.robotPos.curDir}`
        },

        // turn right command
        turnRight(robotPos) {
            if (robotPos.curDir === "North") {
                robotPos.curDir = "East";
            } else if (robotPos.curDir === "East") {
                robotPos.curDir = "South";
            } else if (robotPos.curDir === "South") {
                robotPos.curDir = "West";
            } else if (robotPos.curDir === "West") {
                robotPos.curDir = "North";
            } else {
                throw console.error();
            }
        },

        // turn left command
        turnLeft(robotPos) {
            if (robotPos.curDir === "North") {
                robotPos.curDir = "West";
            } else if (robotPos.curDir === "East") {
                robotPos.curDir = "North";
            } else if (robotPos.curDir === "South") {
                robotPos.curDir = "East";
            } else if (robotPos.curDir === "West") {
                robotPos.curDir = 'South';
            } else {
                throw console.error();
            }
        },

        // walk forward command
        walkForward(robotPos) {
            if (robotPos.curDir === 'North') {
                robotPos.curY -= 1
                if (robotPos.curY < 0) {
                    throw console.error();
                }
            } else if (robotPos.curDir === 'East') {
                robotPos.curX += 1
                if (robotPos.curX >= this.maxX) {
                    throw console.error();
                }
            } else if (robotPos.curDir === 'South') {
                robotPos.curY += 1
                if (robotPos.curY >= this.maxY) {
                    throw console.error();
                }
            } else if (robotPos.curDir === 'West') {
                robotPos.curX -= 1
                if (robotPos.curX < 0) {
                    throw console.error();
                }
            } else {
                throw console.error();
            }
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home {
    background-color: #EFE4FF;
    height: 100vh;
}
h1 {
  text-align: center;
  color: #330088;
  font-size: 48px;
};
.centered-input input {
  text-align: center;
}
</style>
