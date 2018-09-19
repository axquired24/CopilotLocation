<template>
    <section class="content">
        <div class="row">
            <div class="col-md-3">
                <FilterLocation :doSubmit="submitEvent"></FilterLocation>
            </div>
            <div class="col-md-9">
                <SimpleMapBox ref="petaSimple"></SimpleMapBox>
            </div>
        </div>
    </section>
</template>

<script>
    import FilterLocation from '@/components/base/FilterLocation.vue'
    import SimpleMapBox from '@/components/base/SimpleMapBox.vue'
    // import EVENT_UTIL from '@/components/EventUtil'
    const usStatesUrl = "https://api.myjson.com/bins/qv384"

    export default {
        name: 'Maps',
        components: {
            FilterLocation,
            SimpleMapBox
        },
        data: () => {
            return {
                submitEvent: "loadPetaSimple"
            }
        },
        mounted() {
            this.$root.$on(this.submitEvent, (data) => {
                this.loadMap(this)
            })
        },
        methods: {
            loadMap: (self) => {
                let mapBoxObject = self.$refs.petaSimple
                mapBoxObject.loadMapBackground(mapBoxObject, 37.8, -106, 4)
                mapBoxObject.loadGeoJson(mapBoxObject, usStatesUrl)
            }
        }
    }
</script>
