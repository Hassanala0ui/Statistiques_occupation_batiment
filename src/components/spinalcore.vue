<template>
  
  <room-count-box :room-count="selectedFloor ? selectedFloor.children.length : 0"></room-count-box>
  <filter-and-search-bar @search="handleSearch"></filter-and-search-bar>
  <H1>Statistiques d'occupation du bâtiment : </H1>
  <div class="container">
    <h1 class="text-left" v-if="building">{{ building.name }}</h1>
    <div class="row">
      <div class="col-md-4">
        <div class="btn-group" role="group">
          <button
            v-for="floor in floors"
            :key="floor.dynamicId"
            class="btn btn-primary"
            @click="selectedFloor = floor">
            {{ floor.name }} - {{ floor.occupancyPercentage }}%
          </button>
        </div>
      </div>
      <div class="col-md-8"> 
        <div v-if="selectedFloor">
          <h2>Statut d'occupation {{ selectedFloor.name }}</h2>
          <ul class="list-group">
            <li
              v-for="room in filteredRooms"
              :key="room.dynamicId"
              class="list-group-item"
            >
              {{ room.name }} -
              <span
                v-if="pieces.find(piece => piece.roomId === room.dynamicId)?.occupancy !== undefined"
                :class="{
                  'text-danger': pieces.find(piece => piece.roomId === room.dynamicId)?.occupancy,
                  'text-success': !pieces.find(piece => piece.roomId === room.dynamicId)?.occupancy,
                }"
              >
                {{ pieces.find(piece => piece.roomId === room.dynamicId)?.occupancy ? 'Occupé' : 'Non occupé' }}
              </span>
              <span v-else class="text-muted">Undefined</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
    <pie_chart :occupancy-percentage="selectedFloor ? selectedFloor.occupancyPercentage : 0"></pie_chart>
</template>

<script>
import RoomCountBox from './RoomCountBox.vue';
import FilterAndSearchBar from './FilterAndSearchBar.vue';
import pie_chart from './pie-chart.vue';
export default {
  components: {
    RoomCountBox,
    FilterAndSearchBar,
    pie_chart,
  },
data() {
  return {
    geographicContext: null,
    pieces: [],
    selectedFloor: null,
    isLoading: true,
    filterStatus: '',
    searchTerm: ''
  };
},
created() {
  this.loadGeographicContextData();
},
methods: {
  loadGeographicContextData() {
    this.isLoading = true;
    fetch('./gegraphicContext_space.json')
      .then((response) => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then((data) => {
        this.geographicContext = data;
        this.isLoading = false;
        const floors = this.floors;
        floors.forEach((floor) => {
          floor.occupancyPercentage = 0;
          floor.children.forEach((room) => {
            this.loadPieceData(room.dynamicId);
          });
        });
      })
      .catch((error) => {
        console.error('Error loading data:', error);
        this.isLoading = false;
      });
  },
 
  loadPieceData(roomId) {
const apiEndpoint = `https://api-developers.spinalcom.com/api/v1/room/${roomId}/control_endpoint_list`;
fetch(apiEndpoint)
  .then((response) => {
    if (!response.ok) {
      throw new Error(`Network response was not ok: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    const endpoints = data[0]?.endpoints; // Use optional chaining to avoid errors
    const occupancyEndpoint = endpoints?.find(
      (endpoint) => endpoint.type === 'Occupation'
    );
    if (occupancyEndpoint) {
      const piece = {
        roomId: roomId,
        occupancy: occupancyEndpoint.currentValue,
      };
      this.pieces.push(piece);
      this.updateFloorOccupancy(roomId);
    } else {
      const piece = {
        roomId: roomId,
        occupancy: undefined,
      };
      this.pieces.push(piece);
      this.updateFloorOccupancy(roomId);
    }
  })
  .catch((error) => {
    console.error(`Error loading piece data for room ${roomId}:`, error);
    // Set occupancy to undefined in case of an error
    const piece = {
      roomId: roomId,
      occupancy: undefined,
    };
    this.pieces.push(piece);
    this.updateFloorOccupancy(roomId);
  });
},
updateFloorOccupancy(roomId) {
const floors = this.floors;
const piece = this.pieces.find((piece) => piece.roomId === roomId);

// Reset occupancyPercentage for all floors
floors.forEach((floor) => {
  floor.occupancyPercentage = 0;
});

// Update occupancyPercentage for each floor
floors.forEach((floor) => {
  let occupiedRooms = 0;
  floor.children.forEach((room) => {
    const roomPiece = this.pieces.find((p) => p.roomId === room.dynamicId);
    if (roomPiece && roomPiece.occupancy) {
      occupiedRooms++;
    }
  });
  floor.occupancyPercentage = parseFloat(((occupiedRooms / floor.children.length) * 100).toFixed(1));
});

// Trigger a re-render of the component
this.$forceUpdate();
},
handleSearch(searchTerm, filterStatus) {
    this.searchTerm = searchTerm;
    this.filterStatus = filterStatus; 
  },
  handleFilter(filterStatus) {
    this.filterStatus = filterStatus; // Update the filter status
  },
},
computed: {
  building() {
    return this.geographicContext ? this.geographicContext.children[0] : null;
  },
  floors() {
    return this.building ? this.building.children : [];
  },
  filteredRooms() {
    if (!this.selectedFloor) return []; // Return an empty array if no floor is selected

    let rooms = this.selectedFloor.children;

    // Filter by status if a status is selected
    if (this.filterStatus) {
  rooms = rooms.filter(room => {
    const piece = this.pieces.find(piece => piece.roomId === room.dynamicId);
    if (piece) {
      if (this.filterStatus === 'Occupé') {
        return piece.occupancy === true;
      } else if (this.filterStatus === 'Libre') {
        return piece.occupancy === false;
      } else if (this.filterStatus === 'Undefined') {
        return piece.occupancy === undefined;
      }
    } else {
      return this.filterStatus === 'Undefined';
    }
  });
}

    // Filter by search term if a search term is provided
    if (this.searchTerm) {
      rooms = rooms.filter(room => room.name.toLowerCase().includes(this.searchTerm.toLowerCase()));
    }

    return rooms;
  }
}
};
</script>

<style scoped>
body{
background-color: rgb(35, 247, 7);
}
H1{
  
  text-align: center;
  color: #060710;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;

}
.container {
max-width: 44%; /* Limit the width to 45% of the page */
margin: 0 auto; /* Center the container */
padding: 20px;
background-color: #f0f0f0;
border: 1px solid #ddd;
border-radius: 8px;
box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
position: relative;
}

.text-left {
text-align: center
}

.btn-group {
margin-bottom: 20px;
}

.btn-primary {
display: inline-block;

background: #b98c03;
border: none;
color: #fff;
padding: 10px 20px;
border-radius: 4px;
transition: background 3s ease;
margin: 0 9%;
}

.btn-primary:active{
background-color: #0177fd;
}

.btn-primary:last-child {
margin-right: 0; /* Remove margin from the last button */
}

.btn-primary:hover {
background: linear-gradient(to right, #FFA000, #b98c03);
}

.list-group {
color: #333;
list-style-type: none;
padding: 0;
}

.list-group-item {
border: 1px solid #ddd;
border-radius: 6px;
padding: 15px;
margin-bottom: 10px;
transition: background 0.3s ease;
}

.list-group-item:hover {
background-color: #f5f5f5;
}

.text-success {
color: #fff;
background-color: #4CAF50;
padding: 4px 12px;
border-radius: 20px;
margin-left: 10px;
font-weight: bold;
}

.text-danger {
color: #fff;
background-color: #F44336;
padding: 4px 12px;
border-radius: 20px;
margin-left: 10px;
font-weight: bold;
}

.text-muted {
color: #fff;
background-color: #ca8a03;
padding: 4px 12px;
border-radius: 20px;
font-weight: bold;
}
</style>