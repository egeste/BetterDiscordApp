<template>
    <Modal class="bd-installModal" :headertext="modal.title" :closing="modal.closing" @close="modal.close" :noheader="true" :class="{'bd-err': err, 'bd-installModalFail': err}">
        <template v-if="err">
            <div slot="body" class="bd-installModalBody">
                <h3>Something went wrong :(</h3>
                <MiError />
            </div>
            <div slot="footer" class="bd-installModalFooter bd-installModalErrMsg">
                {{err.message || err}}
                <span>Ctrl+Shift+I</span>
            </div>
        </template>
        <div v-else-if="loadingInfo" class="bd-spinnerContainer" slot="body">
            <span>Loading Remote Package</span>
            <div class="bd-spinner7"></div>
        </div>
    </Modal>
</template>

<script>
    // Imports
    import { Modal, MiExtension, MiSuccessCircle, MiError } from '../../common';
    import { PluginManager, ThemeManager, PackageInstaller, Settings } from 'modules';

    export default {
        data() {
            return {
                loadingInfo: true,
                upToDate: true,
                allowUnsafe: Settings.getSetting('security', 'default', 'unsafe-content').value,
                installed: false,
                err: null
            }
        },
        props: ['modal'],
        components: {
            Modal, MiExtension, MiSuccessCircle, MiError
        },
        methods: {
            async loadRemote() {
                try {
                    const info = await PackageInstaller.downloadRemotePackage(this.modal.remoteLocation);
                    this.modal.confirm(info.outputPath);
                    this.modal.close();
                } catch (err) {
                    this.err = err;
                }
            }
        },
        mounted() {
            this.loadRemote();
        }
    }
</script>
