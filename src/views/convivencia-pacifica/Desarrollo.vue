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

const comisionConstruccion = ref();
const tema = ref();
const temaPromover = ref();
const comisionAprobacion = ref();
const institucionEducativa = ref();
const temaDisciplinario = ref();

const miembrosComisionConstruccion = ref();



const form: any = ref({
    sie: null,
    departamentoId: null,
    departamentoNombre: '',
    municipioId: null,
    municipioNombre: '',
    unidadEducativa: '',
    nivel: '',
    modalidad: '',
    director: '',
    fecha: '',
    registroAnterior: false,
    comisionCargo: '',
    comisionNombre: '',
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
    temaDerecho: '',
    temaNorma: '',
    temaDisciplinario: '',
    temaDisciplinarioCorrectivo: '',
    temaDisciplinarioProcedimientoMarco: '',
    temaDisciplinarioProcedimientoAlternativo: '',
    temaDisciplinarioLineamiento: '',
    temaSancion: '',
    temaAdopcion: '',
    temaAlternativo: '',
    temaRemision: '',
    temaTaller: '',
    temaPromover: '',
    temaPromover1: '',
    temaPromover2: '',
    temaPromover3: '',
    temaPromover4: '',
    temaPromover5: '',
    temaPromover6: '',
    temaPromover7: '',
    temaPromover8: '',
    temaPromover9: '',
    temaSeguimiento: '',
    comisionAprobacionCargo: '',
    comisionAprobacionNombre: '',
    comisionAprobacionEstudiante: false,
    comisionAprobacionDirector: false,
    comisionAprobacionMaestro: false,
    comisionAprobacionPadre: false,
    comisionAprobacionOtro: false,
    comisionAprobacionEstudianteNombre: '',
    comisionAprobacionDirectorNombre: '',
    comisionAprobacionMaestroNombre: '',
    comisionAprobacionPadreNombre: '',
    comisionAprobacionOtroNombre: '',
    fechaAprobacion: '',
    vigenciaAprobacion: '',
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
        findInstitucionEducativa();
        findMiembrosComisionConstruccion();


    }
}); 

const findInstitucionEducativa = async () => {
    console.log(form.value.sie);
    if(String(form.value.sie).length === 8){
        const res = await ConvivenciaPacifica.findInstitucionEducativa(form.value.sie);
        console.log("res", res);
        if(res.data && res.data.length > 0){
            form.value.departamentoId = res.data[0].departamento_codigo;
            form.value.departamentoNombre = 'ronal'; //res.data[0].departamento;
            form.value.municipioId = res.data[0].municipio_codigo;  
            form.value.municipioNombre = res.data[0].municipio;
            form.value.unidadEducativa = res.data[0].institucioneducativa;
            form.value.nivel = res.data[0].niveles_abrv;
            form.value.modalidad = res.data[0].dependencia;
            form.value.director = res.data[0].director;
            find.value = true;
            institucionEducativa.value = res.data[0];
            console.log(res.data[0], form.value.sie.length, res);
        }
    } else {
        institucionEducativa.value = null;
        find.value = false;
        form.value.departamentoId = null;
        form.value.departamentoNombre = '';
        form.value.municipioId = null;
        form.value.municipioNombre = '';
        form.value.unidadEducativa = '';
        form.value.nivel = '';
        form.value.modalidad = '';
        form.value.director = '';
    }
}; 

const findMiembrosComisionConstruccion = async () => {
    console.log(form.value.sie);
    if(String(form.value.sie).length === 8){
        const res = await ConvivenciaPacifica.findMiembrosComisionConstruccion(form.value.sie);
        console.log("res", res);
        if(res.data && res.data.length > 0){
          //  form.value.comisionSocializacionEstudiante= res.data[0].  ;       
            form.value.comisionSocializacionEstudianteNombre= res.data[0].nombres_miembro ; 
         //   form.value.comisionSocializacionDirector= res.data[0].  ;         
            form.value.comisionSocializacionDirectorNombre= res.data[0].nombres_director  ;   
         //   form.value.comisionSocializacionMaestro= res.data[0].  ;          
            form.value.comisionSocializacionMaestroNombre= res.data[0].nombres_miembro  ;    
         //   form.value.comisionSocializacionPadre= res.data[0].  ;            
            form.value.comisionSocializacionPadreNombre= res.data[0].nombres_miembro  ;      
         //   form.value.comisionSocializacionOtro= res.data[0].  ;             
            form.value.comisionSocializacionOtroNombre= res.data[0].nombres_miembro  ;    


            miembrosComisionConstruccion.value = res.data[0];
            console.log(res.data[0], form.value.sie.length, res);
        }
    } else {
        miembrosComisionConstruccion.value = null;
        find.value = false;
        form.value.departamentoId = null;
        form.value.departamentoNombre = '';
        form.value.municipioId = null;
        form.value.municipioNombre = '';
        form.value.unidadEducativa = '';
        form.value.nivel = '';
        form.value.modalidad = '';
        form.value.director = '';
    }
}; 


const onDateInput = (event: any) => {
    // Remove non-numeric characters from the input
    const cleanedInput = event.target.value.replace(/\D/g, '');

    // Format the input as a date (DD-MM-YYYY)
    if (cleanedInput.length <= 2) {
        form.value.fecha = cleanedInput;
    } else if (cleanedInput.length <= 4) {
        form.value.fecha = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2);
    } else if (cleanedInput.length <= 8) {
        form.value.fecha = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
    } else {
        form.value.fecha = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
    }
};

const onDateInputAprobacion = (event: any) => {
    // Remove non-numeric characters from the input
    const cleanedInput = event.target.value.replace(/\D/g, '');

    // Format the input as a date (DD-MM-YYYY)
    if (cleanedInput.length <= 2) {
        form.value.fechaAprobacion = cleanedInput;
    } else if (cleanedInput.length <= 4) {
        form.value.fechaAprobacion = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2);
    } else if (cleanedInput.length <= 8) {
        form.value.fechaAprobacion = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
    } else {
        form.value.fechaAprobacion = cleanedInput.slice(0, 2) + '/' + cleanedInput.slice(2, 4) + '/' + cleanedInput.slice(4, 8);
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

    comisionConstruccion.value = {
        1: {status: form.value.comisionSocializacionEstudiante, value: form.value.comisionSocializacionEstudianteNombre},
        2: {status: form.value.comisionSocializacionDirector, value: form.value.comisionSocializacionDirectorNombre},
        3: {status: form.value.comisionSocializacionMaestro, value: form.value.comisionSocializacionMaestroNombre},
        4: {status: form.value.comisionSocializacionPadre, value: form.value.comisionSocializacionPadreNombre},
        5: {status: form.value.comisionSocializacionOtro, value: form.value.comisionSocializacionOtroNombre}
    };

    tema.value = {
        1: form.value.temaDerecho,
        2: form.value.temaNorma,
        3: form.value.temaDisciplinario,
        4: form.value.temaSancion,
        5: form.value.temaAdopcion,
        6: form.value.temaAlternativo,
        7: form.value.temaRemision,
        8: form.value.temaTaller,
        9: form.value.temaPromover,
        10: form.value.temaSeguimiento
    };

    temaPromover.value = {
        1: form.value.temaPromover1,
        2: form.value.temaPromover2,
        3: form.value.temaPromover3,
        4: form.value.temaPromover4,
        5: form.value.temaPromover5,
        6: form.value.temaPromover6,
        7: form.value.temaPromover7,
        8: form.value.temaPromover8,
        9: form.value.temaPromover9
    };

    temaDisciplinario.value = {
        10: form.value.temaDisciplinarioCorrectivo,
        11: form.value.temaDisciplinarioProcedimientoMarco,
        12: form.value.temaDisciplinarioProcedimientoAlternativo,
        13: form.value.temaDisciplinarioLineamiento,
    };

    comisionAprobacion.value = {
        1: {status: form.value.comisionAprobacionEstudiante, value: form.value.comisionAprobacionEstudianteNombre},
        2: {status: form.value.comisionAprobacionDirector, value: form.value.comisionAprobacionDirectorNombre},
        3: {status: form.value.comisionAprobacionMaestro, value: form.value.comisionAprobacionMaestroNombre},
        4: {status: form.value.comisionAprobacionPadre, value: form.value.comisionAprobacionPadreNombre},
        5: {status: form.value.comisionAprobacionOtro, value: form.value.comisionAprobacionOtroNombre}
    };

    const payload1 = {
        cod_ue: form.value.sie,
        desc_ue: form.value.unidadEducativa, 
        cod_sie: form.value.sie,
        cod_rda_director: null,
        cod_director: null,
        nombres_director: form.value.director,
        apellidos_director: form.value.director,
    
        cod_departamento: 1,
        desc_departamento: 'Chuquisaca',
        cod_municipio: 1,
        desc_municipio: 'Sucre',
        cod_nivel: 13,
        desc_nivel: 'Secundaria',
        modalidad: 'Presencial',
    
        estado: 'ACTIVO',
        usu_cre: 1,
        fec_cre: new Date()
    }

    console.log(payload1);

    const save1 = await ConvivenciaPacifica.create(payload1).then((res) => {
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
        console.log("save1", save1);

    const payload2 = {
        id_pcpa_unidad_educativa: save1.data.id,
        fecha_registro: null,    
        check_diagnostico_pcpa: true,
        estado: 'ACTIVO',
        usu_cre: "1",
        fec_cre: new Date()
    }
    
    const save2 = await ConvivenciaPacifica.createContruccion(payload2).then((res) => {
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

    console.log("save2", save2);
    console.log( comisionConstruccion.value);

    console.log("ini bucle ");
    let payload3;
    let save3;
    await Object.keys(comisionConstruccion.value).map((item, key) => {
        if(comisionConstruccion.value[item].status){
            console.log(item, key);
            payload3 = {
                id_pcpa_construccion: save2.data.id,
                id_pcpa_comision_tipo: item,
                id_pcpa_miembro_tipo: 1,
                orden: key + 1,
                nombres_miembro: comisionConstruccion.value[item].value,
                apellidos_miembro: comisionConstruccion.value[item].value, 
                check_miembro_comision: comisionConstruccion.value[item].status,                    
                estado: 'ACTIVO' ,
                usu_cre: "1",
                fec_cre: new Date()
            }

            save3 = ConvivenciaPacifica.createMiembroComision(payload3).then((res) => {
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
        }        
    });
    console.log("fin bucle ");
    console.log("save3", save3);

    console.log("ini bucle ");
    let payload4;
    let save4;
    await Object.keys(tema.value).map((item, key) => {
        if(tema.value[item]){
            console.log(item, key);
            payload4 = {
                id_pcpa_construccion: save2.data.id,
                id_pcpa_actividades_tipo: item,                   
                nivel: 1,
                fec_aprobacion: null,
                tiempo_vigencia: 0,
                declaracion_jurada: true,                    
                estado: 'ACTIVO' ,
                usu_cre: '1',
                fec_cre: new Date()
            }

            save4 = ConvivenciaPacifica.createTarea(payload4).then((res) => {
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
        }        
    });
    console.log("fin bucle ");
    console.log("save4", save4);

    if(form.value.temaDisciplinario){
        console.log("ini bucle 50");
        let payload50;
        let save50;
        await Object.keys(temaDisciplinario.value).map((item, key) => {
            if(temaDisciplinario.value[item]){
                console.log(item, key);
                payload50 = {
                    id_pcpa_construccion: save2.data.id,
                    id_pcpa_actividades_tipo: item,                   
                    nivel: 2,
                    fec_aprobacion: null,
                    tiempo_vigencia: 0,
                    declaracion_jurada: true,                    
                    estado: 'ACTIVO' ,
                    usu_cre: '1',
                    fec_cre: new Date()
                }

                save50 = ConvivenciaPacifica.createTareaPromover(payload50).then((res) => {
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
            }        
        });
        console.log("fin bucle ");
        console.log("save50", save50);
    }

    if(form.value.temaPromover){
        console.log("ini bucle ");
        let payload5;
        let save5;
        await Object.keys(temaPromover.value).map((item, key) => {
            if(temaPromover.value[item]){
                console.log(item, key);
                payload5 = {
                    id_pcpa_construccion: save2.data.id,
                    id_pcpa_actividades_tipo: item,                   
                    nivel: 2,
                    fec_aprobacion: null,
                    tiempo_vigencia: 0,
                    declaracion_jurada: true,                    
                    estado: 'ACTIVO' ,
                    usu_cre: '1',
                    fec_cre: new Date()
                }

                save5 = ConvivenciaPacifica.createTareaPromover(payload5).then((res) => {
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
            }        
        });
        console.log("fin bucle ");
        console.log("save5", save5);
    }

    console.log("ini bucle ");
    let payload6;
    let save6;
    await Object.keys(comisionConstruccion.value).map((item, key) => {
        if(comisionConstruccion.value[item].status){
            console.log(item, key);
            payload6 = {
                id_pcpa_construccion: save2.data.id,
                id_pcpa_comision_tipo: item,
                id_pcpa_miembro_tipo: 2,
                orden: key + 1,
                nombres_miembro: comisionAprobacion.value[item].value,
                apellidos_miembro: comisionAprobacion.value[item].value, 
                check_miembro_comision: comisionAprobacion.value[item].status,                    
                estado: 'ACTIVO' ,
                usu_cre: "1",
                fec_cre: new Date()
            }

            save6 = ConvivenciaPacifica.createMiembroComisionAprobacion(payload6).then((res) => {
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
        }        
    });
    console.log("fin bucle ");
    console.log("save6", save6);

    console.log("fin");
      
};

const reset = () => {    
    form.value.fecha = '';
    form.value.registroAnterior = false;
    form.value.comisionCargo = '';
    form.value.comisionNombre = '';
    form.value.comisionSocializacionEstudiante = false;
    form.value.comisionSocializacionDirector = false;
    form.value.comisionSocializacionMaestro = false;
    form.value.comisionSocializacionPadre = false;
    form.value.comisionSocializacionOtro = false;
    form.value.comisionSocializacionEstudianteNombre = '';
    form.value.comisionSocializacionDirectorNombre = '';
    form.value.comisionSocializacionMaestroNombre = '';
    form.value.comisionSocializacionPadreNombre = '';
    form.value.comisionSocializacionOtroNombre = '';
    form.value.temaDerecho = '';
    form.value.temaNorma = '';
    form.value.temaDisciplinario = '';
    form.value.temaDisciplinarioCorrectivo = '';
    form.value.temaDisciplinarioProcedimientoMarco = '';
    form.value.temaDisciplinarioProcedimientoAlternativo = '';
    form.value.temaDisciplinarioLineamiento = '';        
    form.value.temaSancion = '';
    form.value.temaAdopcion = '';
    form.value.temaAlternativo = '';
    form.value.temaRemision = '';
    form.value.temaTaller = '';
    form.value.temaPromover = '';
    form.value.temaPromover1 = '';
    form.value.temaPromover2 = '';
    form.value.temaPromover3 = '';
    form.value.temaPromover4 = '';
    form.value.temaPromover5 = '';
    form.value.temaPromover6 = '';
    form.value.temaPromover7 = '';
    form.value.temaPromover8 = '';
    form.value.temaPromover9 = '';
    form.value.temaSeguimiento = '';
    form.value.comisionAprobacionCargo = '';
    form.value.comisionAprobacionNombre = '';
    form.value.comisionAprobacionEstudiante = false;
    form.value.comisionAprobacionDirector = false;
    form.value.comisionAprobacionMaestro = false;
    form.value.comisionAprobacionPadre = false;
    form.value.comisionAprobacionOtro = false;
    form.value.comisionAprobacionEstudianteNombre = '';
    form.value.comisionAprobacionDirectorNombre = '';
    form.value.comisionAprobacionMaestroNombre = '';
    form.value.comisionAprobacionPadreNombre = '';
    form.value.comisionAprobacionOtroNombre = '';
    form.value.fechaAprobacion = '';
    form.value.vigenciaAprobacion = '';
    form.value.validado = false;
    dialogSave.value = false;
};

const validateForm = () => {
    validationErrors.value = {};

    if (!form.value.sie || !form.value.unidadEducativa) validationErrors.value['sie'] = true;
    else delete validationErrors.value['sie'];

    if (!form.value.fechaAprobacion) validationErrors.value['fecha'] = true;
    else delete validationErrors.value['fecha'];

    if (!form.value.comisionSocializacionEstudiante && !form.value.comisionSocializacionDirector && !form.value.comisionSocializacionMaestro && !form.value.comisionSocializacionPadre && !form.value.comisionSocializacionOtro) validationErrors.value['comision'] = true;
    else delete validationErrors.value['comision'];

    if (!form.value.temaDerecho && !form.value.temaNorma && !form.value.temaDisciplinario && !form.value.temaSancion && !form.value.temaAdopcion && !form.value.temaAlternativo && !form.value.temaRemision && !form.value.temaTaller && !form.value.temaPromover && !form.value.temaSeguimiento) validationErrors.value['tema'] = true;
    else delete validationErrors.value['tema'];

    if(form.value.temaPromover){
        if (!form.value.temaPromover1 && !form.value.temaPromover2 && !form.value.temaPromover3 && !form.value.temaPromover4 && !form.value.temaPromover5 && !form.value.temaPromover6 && !form.value.temaPromover7 && !form.value.temaPromover8 && !form.value.temaPromover9) validationErrors.value['temaPromover'] = true;
        else delete validationErrors.value['temaPromover'];
    }

    if (!form.value.comisionAprobacionEstudiante && !form.value.comisionAprobacionDirector && !form.value.comisionAprobacionMaestro && !form.value.comisionAprobacionPadre && !form.value.comisionAprobacionOtro) validationErrors.value['comisionAprobacion'] = true;
    else delete validationErrors.value['comisionAprobacion'];

    if (!form.value.fechaAprobacion) validationErrors.value['fechaAprobacion'] = true;
    else delete validationErrors.value['fechaAprobacion'];

    if (!form.value.fechaAprobacion) validationErrors.value['vigenciaAprobacion'] = true;
    else delete validationErrors.value['vigenciaAprobacion'];

    if (!form.value.validado) validationErrors.value['validado'] = true;
    else delete validationErrors.value['validado'];
   
    return !Object.keys(validationErrors.value).length;
};


</script>
<template>
    <v-row>    
        <v-col cols="12" lg="12" sm="12">
            <v-card elevation="10" class="withbg">
                <v-card-item>
                    <div class="d-sm-flex align-center justify-space-between pt-sm-2">
                        <v-card-title class="text-h5">Registro de datos</v-card-title>
                    </div>
                    <v-form v-model="valid" class="">
                        <v-container>
                        <v-row>
                            <v-col cols="12" md="12">                                
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Datos de Unidad Educativa</span>
                                </div>
                            </v-col>
                            <v-col cols="12" md="4">
                                <v-text-field v-model="form.sie" :rules="sieRules" :counter="8" label="SIE" required hide-details v-on:keyup="findInstitucionEducativa" :readonly="find && !variusSie"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="8" >
                                <v-text-field v-model="form.unidadEducativa" :counter="10" label="Unidad Educativa" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.departamentoNombre" label="Departamento" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.municipioNombre" label="Municipio" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.nivel" label="Nivel" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.modalidad" label="Modalidad" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="8" >
                                <v-text-field v-model="form.director" label="Director" hide-details required :readonly="find"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12">
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Construcción del PCPA</span>
                                </div>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.fecha" label="Fecha de registro" @input="onDateInput" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="8" >
                                <!-- <v-checkbox v-model="form.registroAnterior" :error-messages="''" label="¿Se realizó un diagnóstico antes de iniciar la construcción del PCPA?" required></v-checkbox> -->
                                <v-checkbox v-model="form.registroAnterior" label="¿Se realizó un diagnóstico antes de iniciar la construcción del PCPA?" required></v-checkbox>
                            </v-col>

                            <!-- <v-col cols="12" md="4" >
                                Miembros de la comisión de construcción del PCPA
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionCargo" label="Cargo" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionNombre" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="12">
                                Miembros de la comisión de construcción del PCPA
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionEstudiante" label="Estudiantes" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionEstudianteNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionSocializacionEstudiante" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionDirector" label="Director(a)" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionDirectorNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionSocializacionDirector" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionMaestro" label="Maestro(a)" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionMaestroNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionSocializacionMaestro" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionPadre" label="Padres/Madres" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionPadreNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionSocializacionPadre" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionSocializacionOtro" label="Otros" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionSocializacionOtroNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionSocializacionOtro" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12" >
                                Actividades para promover la convivencia pacífica
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaDerecho" label="Derechos y deberes" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaNorma" label="Normas y conductas" required></v-checkbox>
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaDisciplinario" label="Procedimientos disciplinarios" required></v-checkbox>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaDisciplinario" label="Procedimientos disciplinarios" required></v-checkbox>
                                <v-row class="pl-10 secondarybg" v-if="form.temaDisciplinario">
                                    <v-col cols="12" md="6" >
                                        <v-checkbox v-model="form.temaDisciplinarioCorrectivo" label="CORRECTIVOS PEDAGÓGICOS" required></v-checkbox>
                                        <v-checkbox v-model="form.temaDisciplinarioProcedimientoMarco" label="PROCEDIMIENTO MARCO PARA LA ADOPCIÓN DE DECISIONES DISCIPLINARIAS" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaDisciplinarioProcedimientoAlternativo" label="PROCEDIMIENTOS ALTERNATIVOS PARA LA SOLUCIÓN DE CONFLICTOS" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaDisciplinarioLineamiento" label="LINEAMIENTOS PARA LA REMISIÓN DE INFORMES SOBRE CASOS DE VIOLENCIA" required></v-checkbox>
                                    </v-col>
                                </v-row>
                            </v-col>
                            <!-- <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaSancion" label="Sanciones" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaAdopcion" label="Procedimiento marco para la adopción de decisiones disciplinarias" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaAlternativo" label="Procedimientos alternativos pra la solución de conflictos" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaRemision" label="Lineamientos para la remisión de informes sobre casos de violencia" required></v-checkbox>
                            </v-col> -->

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaTaller" label="Programación de talleres de capacitación" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaPromover" label="Actividades para promover la convivencia pacífica" required></v-checkbox>
                                <v-row class="pl-10 secondarybg" v-if="form.temaPromover">
                                    <v-col cols="12" md="6" >
                                        <v-checkbox v-model="form.temaPromover1" label="Movilización social" required></v-checkbox>
                                        <v-checkbox v-model="form.temaPromover2" label="Fomento al desarrollo de habilidades y práctica de valores" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover3" label="Capacitación" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover4" label="Medidas de seguridad en la infraestructura" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover5" label="Normas de convivencia en la unidad unidadEducativa" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover6" label="Promoción de la participación de las y los estudiantes" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover7" label="Gestión y articulación con la comunidad educativa" required></v-checkbox>  
                                        <v-checkbox v-model="form.temaPromover8" label="Acción comunal" required></v-checkbox>   
                                        <v-checkbox v-model="form.temaPromover9" label="Acciones para reducción de riesgos en el contexto y en la unidad educativa" required></v-checkbox>
                                    </v-col>
                                </v-row>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-checkbox v-model="form.temaSeguimiento" label="Plan de seguimiento y evaluación" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="12">
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Aprobación del PCPA</span>
                                </div>
                            </v-col>

                            <!-- <v-col cols="12" md="6" >
                                <v-text-field v-model="form.comisionAprobacionCargo" label="Cargo" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-text-field v-model="form.comisionAprobacionNombre" label="Nombre" hide-details required></v-text-field>
                            </v-col> -->

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionAprobacionEstudiante" label="Estudiantes" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionAprobacionEstudianteNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionAprobacionEstudiante" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionAprobacionDirector" label="Director(a)" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionAprobacionDirectorNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionAprobacionDirector" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionAprobacionMaestro" label="Maestro(a)" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionAprobacionMaestroNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionAprobacionMaestro" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionAprobacionPadre" label="Padres/Madres" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionAprobacionPadreNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionAprobacionPadre" ></v-text-field>
                            </v-col>

                            <v-col cols="12" md="2" >
                                <v-checkbox v-model="form.comisionAprobacionOtro" label="Otros" ></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="4" >
                                <v-text-field v-model="form.comisionAprobacionOtroNombre" :counter="10" label="Nombre" hide-details :disabled="!form.comisionAprobacionOtro" ></v-text-field>
                            </v-col>


                            <v-col cols="12" md="6" >
                                <v-text-field v-model="form.fechaAprobacion" label="Fecha de aprobación"  @input="onDateInputAprobacion" placeholder="DD/MM/AAAA" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="6" >
                                <v-text-field v-model="form.vigenciaAprobacion" label="Tiempo de vigencia" type="number" @input="onDateInput" hide-details required></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12">
                                <div class="text-h6 w-100 font-weight-regular auth-divider position-relative">
                                    <span class="bg-surface position-relative text-subtitle-1 text-grey100">Declaración jurada</span>
                                </div>
                            </v-col>

                            <v-col cols="12" md="12" >
                                <v-checkbox v-model="form.validado" label="Declaro que todos los datos que he registrado son verídicos y que pueden ser validados por las autoridades del Ministerio de Educación" required></v-checkbox>
                            </v-col>

                            <v-col cols="12" md="12" v-if="form.validado" >                                
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
