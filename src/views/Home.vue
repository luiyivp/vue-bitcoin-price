<template>
    <div>
        <v-layout :wrap="true">
            <v-flex xs12>
                <v-card>
                    <v-date-picker
                        v-model="date"
                        full-width
                        :min="min"
                        :max="max"
                        color="info"
                        @change="getBitcoinPrice(date)"
                    />
                </v-card>
                <v-card color="error" dark>
                    <v-card-text class="display-1 text-center">
                        <div v-if="this.dollarValue">
                            US$ {{ this.dollarValue }}
                        </div>
                        <div v-else>
                            {{ this.errorValue }}
                        </div>
                    </v-card-text>
                </v-card>
            </v-flex>
        </v-layout>
    </div>
</template>

<script>
import axios from 'axios';
import { mapMutations } from 'vuex';

export default {
    data: () => ({
        date: new Date().toISOString().substr(0, 10),
        min: '1984',
        max: new Date().toISOString().substr(0, 10),
        dollarValue: '',
        errorValue: ''
    }),
    methods: {
        ...mapMutations(['showLoading', 'dismissLoading']),
        async getBitcoinPrice(date) {
            let arrayDate = date.split('-');
            let ddmmyy = arrayDate[2] + '-' + arrayDate[1] + '-' + arrayDate[0];

            try {
                this.showLoading({ title: 'searching for data' });
                let res = await axios.get(`https://mindicador.cl/api/bitcoin/${ddmmyy}`);
                if (res.data.serie.length > 0) {
                    this.dollarValue = res.data.serie[0].valor;
                } else {
                    this.dollarValue = undefined;
                    this.errorValue = `No value for ${ddmmyy}`;
                }
            } catch (err) {
                console.log(err);
            } finally {
                this.dismissLoading();
            }
        }
    },
    created() {
        this.getBitcoinPrice(this.date);
    }
};
</script>
