<template>
    <section class="content">
        <div class="row">
            <div class="col-md-3">
                <FilterLocation></FilterLocation>
            </div>
            <div class="col-md-9">
                <SimpleMapBox></SimpleMapBox>
            </div>
        </div>
    </section>
</template>

<script>
    import FilterLocation from '@/components/base/FilterLocation.vue'
    import SimpleMapBox from '@/components/base/SimpleMapBox.vue'

    export default {
        name: 'Maps',
        components: {
            FilterLocation,
            SimpleMapBox
        }
    }
</script>
