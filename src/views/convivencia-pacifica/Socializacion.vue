<script setup lang="ts">
import { ref, onMounted } from 'vue';
import UiParentCard from '@/components/shared/UiParentCard.vue';
import { useRouter } from "vue-router";
import { toast } from 'vue3-toastify';
import ConvivenciaPacifica from '@/services/ConvivenciaPacifica';
// import { useNavbarStore } from '@/stores/navbar';
// const store = useNavbarStore();  
// store.setPath('/convivencia/pacifica');
const router = useRouter();

const valid = ref(false);
const dialog = ref(false);
const dialogSave = ref(false);
const validationErrors = ref();
const find = ref(false);
const variusSie = ref(false);

const institucionEducativa = ref();
const miembrosComision = ref();

const form: any = ref({
    sie: null,
    unidadEducativa: '',
    comisionSocializacionEstudiante: false,
    comisionSocializacionDirector: false,
    comisionSocializacionMaestro: false,
    comisionSocializacionPadre: false,
    comisionSocializacionOtro: false,
    comisionSocializacionEstudianteNombre: '',
    comisionSocializacionDirectorNombre: '',
    comisionSocializacionMaestroNombre: '',
    comisionSocializacionPadreNombre: '',
    comisionSocializacionOtroNombre: '',
    comisionImplementacionEstudiante: false,
    comisionImplementacionDirector: false,
    comisionImplementacionMaestro: false,
    comisionImplementacionPadre: false,
    comisionImplementacionOtro: false,
    comisionImplementacionEstudianteNombre: '',
    comisionImplementacionDirectorNombre: '',
    comisionImplementacionMaestroNombre: '',
    comisionImplementacionPadreNombre: '',
    comisionImplementacionOtroNombre: '',
    actividad1: null,
    actividad2: null,
    actividad3: null,
    actividad4: null,
    actividad5: null,
    actividad1Fecha: '',
    actividad2Fecha: '',
    actividad3Fecha: '',
    actividad4Fecha: '',
    actividad5Fecha: '',    
    validado: false
});

const sieRules = [
    (value: any) => {
        if (value) return true
        return 'El SIE es requerido'
    },
    (value: any) => {
        if (value?.length === 8) return true
        return 'El código SIE requiere 8 dígitos.'
    },
];

onMounted(async() => {
    let user = JSON.parse(localStorage.getItem('user') || '');
    if(user && user.codigo_sie){
        form.value.sie = user.codigo_sie;
        const resInst = await findInstitucionEducativa();
        const resMiem = await findMiembroComision();
    }
}); 

const findInstitucionEducativa = async () => {
    console.log(form.value.sie);
    if(String(form.value.sie).length === 8){
        const res = await ConvivenciaPacifica.findInstitucionEducativa(form.value.sie);
        console.log("res", res);
        if(res.data && res.data.length > 0){
            form.value.unidadEducativa = res.data[0].institucioneducativa;
            find.value = true;
            institucionEducativa.value = res.data[0];
        }
    } else {
        institucionEducativa.value = null;
        find.value = false;
        form.value.unidadEducativa = '';
    }
}; 

const findMiembroComision = async () => {
    console.log(form.value.sie);
    if(String(form.value.sie).length === 8){
        const res = await ConvivenciaPacifica.listMiembroComision(form.value.sie);
        console.log("res", res);
        if(res.data && res.data.length > 0){

            const idComisionSocializacionEstudiante = (obj: any) => obj.id_comision_tipo === 1 && obj.id_miembro_tipo === 1;
            const idComisionSocializacionDirector = (obj: any) => obj.id_comision_tipo === 2 && obj.id_miembro_tipo === 1;
            const idComisionSocializacionMaestro = (obj: any) => obj.id_comision_tipo === 3 && obj.id_miembro_tipo === 1;
            const idComisionSocializacionPadre = (obj: any) => obj.id_comision_tipo === 4 && obj.id_miembro_tipo === 1;
            const idComisionSocializacionOtro = (obj: any) => obj.id_comision_tipo === 5 && obj.id_miembro_tipo === 1;
            form.value.comisionSocializacionEstudiante = res.data.some(idComisionSocializacionEstudiante);
            form.value.comisionSocializacionDirector = res.data.some(idComisionSocializacionDirector);
            form.value.comisionSocializacionMaestro = res.data.some(idComisionSocializacionMaestro);
            form.value.comisionSocializacionPadre = res.data.some(idComisionSocializacionPadre);
            form.value.comisionSocializacionOtro = res.data.some(idComisionSocializacionOtro);
            form.value.comisionSocializacionEstudianteNombre = res.data.find(idComisionSocializacionEstudiante)?.nombres_miembro;
            form.value.comisionSocializacionDirectorNombre = res.data.find(idComisionSocializacionDirector)?.nombres_miembro;
            form.value.comisionSocializacionMaestroNombre = res.data.find(idComisionSocializacionMaestro)?.nombres_miembro;
            form.value.comisionSocializacionPadreNombre = res.data.find(idComisionSocializacionPadre)?.nombres_miembro;
            form.value.comisionSocializacionOtroNombre = res.data.find(idComisionSocializacionOtro)?.nombres_miembro;

            
            const idComisionImplementacionEstudiante = (obj: any) => obj.id_comision_tipo === 1 && obj.id_miembro_tipo === 2;
            const idComisionImplementacionDirector = (obj: any) => obj.id_comision_tipo === 2 && obj.id_miembro_tipo === 2;
            const idComisionImplementacionMaestro = (obj: any) => obj.id_comision_tipo === 3 && obj.id_miembro_tipo === 2;
            const idComisionImplementacionPadre = (obj: any) => obj.id_comision_tipo === 4 && obj.id_miembro_tipo === 2;
            const idComisionImplementacionOtro = (obj: any) => obj.id_comision_tipo === 5 && obj.id_miembro_tipo === 2;
            form.value.comisionImplementacionEstudiante = res.data.some(idComisionImplementacionEstudiante);
            form.value.comisionImplementacionDirector = res.data.some(idComisionImplementacionDirector);
            form.value.comisionImplementacionMaestro = res.data.some(idComisionImplementacionMaestro);
            form.value.comisionImplementacionPadre = res.data.some(idComisionImplementacionPadre);
            form.value.comisionImplementacionOtro = res.data.some(idComisionImplementacionOtro);
            form.value.comisionImplementacionEstudianteNombre = res.data.find(idComisionImplementacionEstudiante)?.nombres_miembro;
            form.value.comisionImplementacionDirectorNombre = res.data.find(idComisionImplementacionDirector)?.nombres_miembro;
            form.value.comisionImplementacionMaestroNombre = res.data.find(idComisionImplementacionMaestro)?.nombres_miembro;
            form.value.comisionImplementacionPadreNombre = res.data.find(idComisionImplementacionPadre)?.nombres_miembro;
            form.value.comisionImplementacionOtroNombre = res.data.find(idComisionImplementacionOtro)?.nombres_miembro;

            miembrosComision.value = res.data;
            console.log(res.data);
            console.log(res.data.some(idComisionSocializacionEstudiante))
            console.log(res.data.find(idComisionSocializacionEstudiante)?.nombres_miembro)
        }
    } else {
        miembrosComision.value = null;
    }
}; 

const onDateInput1 = (event: any) => {
    const cleanedInput = event.target.value.replace(/\D/g, '');
    form.value.actividad1Fecha = onDateInput(cleanedInput);
};

const onDateInput2 = (event: any) => {
    const cleanedInput = event.target.value.replace(/\D/g, '');
    form.value.actividad2Fecha = onDateInput(cleanedInput);
};


const onDateInput3 = (event: any) => {
    const cleanedInput = event.target.value.replace(/\D/g, '');
    form.value.actividad3Fecha = onDateInput(cleanedInput);
};


const onDateInput4 = (event: any) => {
    const cleanedInput = event.target.value.replace(/\D/g, '');
    form.value.actividad4Fecha = onDateInput(cleanedInput);
};


const onDateInput5 = (event: any) => {
    const cleanedInput = event.target.value.replace(/\D/g, '');
    form.value.actividad5Fecha = onDateInput(cleanedInput);
};


const onDateInput = (cleanedInput: any) => {
    if (cleanedInput.length <= 2) {
        return cleanedInput;
    } else if (cleanedInput.length <= 4) {
        return cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2);
    } else if (cleanedInput.length <= 8) {
        return cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
    } else {
        return cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
    }
};

const save = async () => {
    console.log(form.value);

    if (!validateForm()) {
        dialog.value = false;  
        toast.info('Debe ingresar los datos requeridos', {
            autoClose: 3000,
            position: toast.POSITION.TOP_RIGHT,
        });
        return false;
    }


    if(form.value.actividad1){
        const payload = {
            id_pcpa_actividades_tipo: form.value.actividad1.id,
            id_pcpa_construccion: miembrosComision.value[0].id,
            cod_actividad: form.value.actividad1.id,  
            desc_actividad: form.value.actividad1.name, 
            fec_actividad: form.value.actividad1Fecha,           
            estado: 'ACTIVO',
            usu_cre: 1,
            fec_cre: new Date()
        }
        console.log('payload1', payload);

        const save = await ConvivenciaPacifica.createSocializacion(payload).then((res) => {
            if(res.status === 201){
                toast.info('Registro guardado correctamente', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                dialog.value = false;  
                dialogSave.value = true; 
                return res;
            } else {
                toast.error('Registro no guardado', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                return res;
            }
        });
        console.log("save1", save);
    }

    if(form.value.actividad2){
        const payload = {
            id_pcpa_actividades_tipo: form.value.actividad2.id,
            id_pcpa_construccion: miembrosComision.value[0].id,
            cod_actividad: form.value.actividad2.id,  
            desc_actividad: form.value.actividad2.name, 
            fec_actividad: form.value.actividad2Fecha,           
            estado: 'ACTIVO',
            usu_cre: 1,
            fec_cre: new Date()
        }
        console.log('payload2', payload);

        const save = await ConvivenciaPacifica.createSocializacion(payload).then((res) => {
            if(res.status === 201){
                toast.info('Registro guardado correctamente', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                dialog.value = false;  
                dialogSave.value = true; 
                return res;
            } else {
                toast.error('Registro no guardado', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                return res;
            }
        });
        console.log("save2", save);
    }

    if(form.value.actividad3){
        const payload = {
            id_pcpa_actividades_tipo: form.value.actividad3.id,
            id_pcpa_construccion: miembrosComision.value[0].id,
            cod_actividad: form.value.actividad3.id,  
            desc_actividad: form.value.actividad3.name, 
            fec_actividad: form.value.actividad3Fecha,           
            estado: 'ACTIVO',
            usu_cre: 1,
            fec_cre: new Date()
        }
        console.log('payload3', payload);

        const save = await ConvivenciaPacifica.createSocializacion(payload).then((res) => {
            if(res.status === 201){
                toast.info('Registro guardado correctamente', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                dialog.value = false;  
                dialogSave.value = true; 
                return res;
            } else {
                toast.error('Registro no guardado', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                return res;
            }
        });
        console.log("save3", save);
    }

    if(form.value.actividad4){
        const payload = {
            id_pcpa_actividades_tipo: form.value.actividad4.id,
            id_pcpa_construccion: miembrosComision.value[0].id,
            cod_actividad: form.value.actividad4.id,  
            desc_actividad: form.value.actividad4.name, 
            fec_actividad: form.value.actividad4Fecha,           
            estado: 'ACTIVO',
            usu_cre: 1,
            fec_cre: new Date()
        }
        console.log('payload4', payload);

        const save = await ConvivenciaPacifica.createSocializacion(payload).then((res) => {
            if(res.status === 201){
                toast.info('Registro guardado correctamente', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                dialog.value = false;  
                dialogSave.value = true; 
                return res;
            } else {
                toast.error('Registro no guardado', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                return res;
            }
        });
        console.log("save4", save);
    }

    if(form.value.actividad5){
        const payload = {
            id_pcpa_actividades_tipo: form.value.actividad5.id,
            id_pcpa_construccion: miembrosComision.value[0].id,
            cod_actividad: form.value.actividad5.id,  
            desc_actividad: form.value.actividad5.name, 
            fec_actividad: form.value.actividad5Fecha,           
            estado: 'ACTIVO',
            usu_cre: 1,
            fec_cre: new Date()
        }
        console.log('payload5', payload);

        const save = await ConvivenciaPacifica.createSocializacion(payload).then((res) => {
            if(res.status === 201){
                toast.info('Registro guardado correctamente', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                dialog.value = false;  
                dialogSave.value = true; 
                return res;
            } else {
                toast.error('Registro no guardado', {
                    autoClose: 3000,
                    position: toast.POSITION.TOP_RIGHT,
                });
                return res;
            }
        });
        console.log("save5", save);
    }
    
    dialog.value = false;  
    dialogSave.value = true;   
};

const reset = () => {
    form.value.actividad1 = null;
    form.value.actividad2 = null;
    form.value.actividad3 = null;
    form.value.actividad4 = null;
    form.value.actividad5 = null;
    form.value.actividad1Fecha = '';
    form.value.actividad2Fecha = '';
    form.value.actividad3Fecha = '';
    form.value.actividad4Fecha = '';
    form.value.actividad5Fecha = '';
    dialogSave.value = false;
};

const actividadTipo = [
    { id: 1, name: 'Carteles' },  
    { id: 2, name: 'Medios de comunicación interna' },  
    { id: 3, name: 'Redes sociales' },  
    { id: 4, name: 'Talleres' },  
    { id: 5, name: 'Ferias' },  
    { id: 6, name: 'Otros' }
]

const validateForm = () => {
    validationErrors.value = {};

    if (!form.value.actividad1 && !form.value.actividad2 && !form.value.actividad3 && !form.value.actividad4 && !form.value.actividad5) validationErrors.value['actividad'] = true;
    else delete validationErrors.value['actividad'];

    if (form.value.actividad1 && !form.value.actividad1Fecha) validationErrors.value['actividad1'] = true;
    else delete validationErrors.value['actividad1'];

    if (form.value.actividad2 && !form.value.actividad2Fecha) validationErrors.value['actividad2'] = true;
    else delete validationErrors.value['actividad2'];

    if (form.value.actividad3 && !form.value.actividad3Fecha) validationErrors.value['actividad3'] = true;
    else delete validationErrors.value['actividad3'];

    if (form.value.actividad4 && !form.value.actividad4Fecha) validationErrors.value['actividad4'] = true;
    else delete validationErrors.value['actividad4'];

    if (form.value.actividad5 && !form.value.actividad5Fecha) validationErrors.value['actividad5'] = true;
    else delete validationErrors.value['actividad5'];
   
    return !Object.keys(validationErrors.value).length;
};

</script>
<template>
    <v-row>    
        <v-col cols="12" lg="12" sm="12">
            <v-card elevation="10" class="withbg">
                <v-card-item>
                    <div class="d-sm-flex align-center justify-space-between pt-sm-2">
                        <v-card-title class="text-h5">Modificación a socialización e implementación </v-card-title>
                    </div>
                    <v-form v-model="valid" class="">
                        <v-container>
                        <v-row>
                            <v-col cols="12" md="12">                                
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Socialización</span>
                                </div>
                            </v-col>
                            <v-col cols="12" md="4">
                                <v-text-field v-model="form.sie" :rules="sieRules" :counter="8" label="SIE" required hide-details v-on:keyup="findInstitucionEducativa" :readonly="find && !variusSie"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="8" >
                                <v-text-field v-model="form.unidadEducativa" :counter="10" label="Unidad Educativa" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12">
                                Miembros de la comisión de socialización del PCPA
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionEstudiante" label="Estudiantes" :readonly="true"></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionEstudianteNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionDirector" label="Director(a)" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionDirectorNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionMaestro" label="Maestro(a)" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionMaestroNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionPadre" label="Padres/Madres" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionPadreNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionOtro" label="Otros" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionOtroNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12">                                
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Actividades para el plan de socialización</span>
                                </div>
                            </v-col>

                            <v-col cols="12" md="12">
                                Miembros de la comisión de implementación del PCPA ¿ Quiénes conforman la comisión de convivencia pacífica y armónica ?
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionImplementacionEstudiante" label="Estudiantes" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionImplementacionEstudianteNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionImplementacionDirector" label="Director(a)" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionImplementacionDirectorNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionImplementacionMaestro" label="Maestro(a)" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionImplementacionMaestroNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionImplementacionPadre" label="Padres/Madres" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionImplementacionPadreNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionImplementacionOtro" label="Otros" :readonly="true" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionImplementacionOtroNombre" :counter="10" label="Nombre" hide-details :readonly="true" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12" >
                                Actividades del Plan de Convivencia Pacífica y Armónica ejecutadas
                            </v-col>

                            <v-col cols="12" md="2" >
                                Actividad 1
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.actividad1" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-select v-model="form.actividad1" :items="actividadTipo" item-title="name" item-value="id" label="Nombre" return-object></v-select>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.actividad1Fecha" label="Fecha"  @input="onDateInput1" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                Actividad 2
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.actividad2" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-select v-model="form.actividad2" :items="actividadTipo" item-title="name" item-value="id" label="Nombre" return-object></v-select>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.actividad2Fecha" label="Fecha"  @input="onDateInput2" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                Actividad 3
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.actividad3" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-select v-model="form.actividad3" :items="actividadTipo" item-title="name" item-value="id" label="Nombre" return-object></v-select>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.actividad3Fecha" label="Fecha"  @input="onDateInput3" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                Actividad 4
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.actividad4" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-select v-model="form.actividad4" :items="actividadTipo" item-title="name" item-value="id" label="Nombre" return-object></v-select>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.actividad4Fecha" label="Fecha"  @input="onDateInput4" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                Actividad 5
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.actividad5" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-select v-model="form.actividad5" :items="actividadTipo" item-title="name" item-value="id" label="Nombre" return-object></v-select>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.actividad5Fecha" label="Fecha"  @input="onDateInput5" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12" >                                
                                <v-dialog v-model="dialog" persistent width="auto" >
                                    <template v-slot:activator="{ props }">                                    
                                        <v-btn size="large" rounded="pill" color="primary" class="rounded-pill" block type="button" flat v-bind="props">Registrar</v-btn>
                                    </template>
                                    <v-card>
                                        <v-card-title class="text-h5">
                                        Confirmar
                                        </v-card-title>
                                        <v-card-text>¿ Está seguro de guardar el registro ?</v-card-text>
                                        <v-card-actions>
                                            <v-spacer></v-spacer>
                                            <v-btn color="green-darken-1" variant="text" @click="dialog = false"> Cancelar </v-btn>
                                            <v-btn color="green-darken-1" variant="text" @click="save"> Aceptar </v-btn>
                                        </v-card-actions>
                                    </v-card>
                                </v-dialog>
                            </v-col>
                        </v-row>
                        </v-container>
                    </v-form>
                </v-card-item>
            </v-card>
        </v-col>
    </v-row>
                                    
    <v-dialog v-model="dialogSave" persistent width="auto" >
        <v-card>
            <v-card-title class="text-h5">
            Mensaje
            </v-card-title>
            <v-card-text>¿ Nuevo registro ?</v-card-text>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green-darken-1" variant="text" @click="router.push('/convivencia/pacifica')"> NO </v-btn>
                <v-btn color="green-darken-1" variant="text" @click="reset"> SI </v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>
