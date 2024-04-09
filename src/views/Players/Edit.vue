<script>
import axios from 'axios'

export default {
    name: 'playerEdit',
    data() {
        return {
            playerId: '',
            errorList: '',
            model: {
                player: {
                    player_name: '',
                    team: '',
                    jersey_number: '',
                    position: '',
                    image: '',
                }
            },
            teams: [],
        }
    },
    mounted() {
        this.fetchTeams();
        this.playerId = this.$route.params.id;
        this.getPlayerData(this.$route.params.id);
    },
    methods: {
        fetchTeams() {
            axios.get('http://127.0.0.1:8000/api/admin/teams').then(res => {
                this.teams = res.data.teams
            });
        },
        getPlayerData(playerId) {
            axios.get(`http://127.0.0.1:8000/api/admin/players/${playerId}/edit`).then(res => {
                console.log(res.data.player);

                this.model.player = res.data.player
            })
            .catch(function (error) {
                    if (error.response) {

                        if(error.response.status == 404) {
                                alert(error.response.data.message);
                        }
                    }
            });
        },
        updatePlayer() {
            var mythis = this;
            axios.put(`http://127.0.0.1:8000/api/admin/players/${this.playerId}/edit`, this.model.player).then(res => {
                console.log(res)
                alert(res.data.message);

                this.errorList = '';
            })
            .catch(function (error) {
                    if (error.response) {

                        if(error.response.status == 422) {
                                mythis.errorList = error.response.data.errors;
                        }
                        else if(error.response.status == 404) {
                                alert(error.response.data.message);
                        }
                    } else if (error.request) {
                        console.log(error.request);
                    } else {
                        console.log('Error', error.message);
                    }
                });
        },
        handleImageUpload(event) {
            this.model.player.image = event.target.files[0];
        }
    },
}
</script>

<template>
    <div class="container mt-5">
        <div class="card">
            <div class="card-header">
                <h4>Edit Player</h4>
            </div>
            <div class="card-body">

                <ul class="alert alert-warning" v-if="Object.keys(this.errorList).length > 0">
                    <li class="mb-0 ms-3" v-for="(error, index) in this.errorList" :key="index">
                        {{ error[0] }}
                    </li>
                </ul>

                <div class="mb-3">
                    <label for="">Player Name</label>
                    <input type="text" v-model="model.player.player_name" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="">Team</label>
                    <select v-model="model.player.team" class="form-select">
                        <option value="" disabled>Select a team</option>
                        <option v-for="team in teams" :key="team.id" :value="team.team_name">{{ team.team_name }}</option>
                    </select>
                </div>
                <div class="mb-3">
                    <label for="">Jersey Number</label>
                    <input type="number" v-model="model.player.jersey_number" class="form-control" max="99" min="0">
                </div>
                <div class="mb-3">
                    <label for="">Position</label>
                    <select v-model="model.player.position" class="form-select">
                        <option value="" disabled>Select a position</option>
                        <option value="PG">Point Guard</option>
                        <option value="SG">Shooting Guard</option>
                        <option value="SF">Small Forward</option>
                        <option value="PF">Power Forward</option>
                        <option value="C">Center</option>
                    </select>
                </div>
                <div class="mb-3">
                    <label for="">Image</label>
                    <input type="file" @change="handleImageUpload" class="form-control">
                </div>
                <div class="mb-3">
                    <span class="update">
                        <button type="button" class="btn btn-success" @click="updatePlayer">Update</button>
                    </span>
                    <RouterLink class="btn btn-secondary" to="/players">Back</RouterLink>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.update {
    margin-right: 10px;
}
</style>