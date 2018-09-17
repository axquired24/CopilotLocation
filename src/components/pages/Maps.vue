<template>
    <section class="content">
        <div class="row">
            <div class="col-md-3">
                <FilterLocation></FilterLocation>
            </div>
            <div class="col-md-9"></div>
        </div>
    </section>
</template>

<script>
    import FilterLocation from '@/components/base/FilterLocation.vue'

    export default {
        name: 'Maps',
        components: {
            FilterLocation
        }
    }
</script>
