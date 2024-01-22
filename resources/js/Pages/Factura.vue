<script setup>

import TopBar from '@/Components/TopBar.vue';
import Switch from '@/Components/Switch.vue';
import CompanyData from '@/Components/CompanyData.vue';
import VueDatePicker from '@vuepic/vue-datepicker';
import InvoiceItem from '@/Components/InvoiceItem.vue'
import '@vuepic/vue-datepicker/dist/main.css';
import { Link } from '@inertiajs/vue3';
import { useDark, useToggle } from '@vueuse/core';

const isDark = useDark({
    onChanged(isDark){
        if(isDark) {
            document.documentElement.classList.add('dark');
        } else {
            document.documentElement.classList.remove('dark');
        }

    }
});
const toggleDark = useToggle(isDark); 


const recEqValues = [5.2, 1.4, 0.5, 1.75];

</script>

<script>
export default {
    data()  {
        return {
            items: [
                {
                    title: 'Una cosa',
                    description: 'lalala',
                    quantity: 1,
                    price: 0,
                    discount: 0,
                    vat: 21,
                    subtotal: 0.0,
                }
            ],
            subtotalFra: 0,
            iva: 0,
            totalFra: 0,
        };
    },
    methods: {
        addItem(){
            let item = {
                title: '',
                description: '',
                quantity: 1,
                price: 0.0,
                discount: 0,
                vat: 21,
                subtotal: 0.0
            };

            this.items.push(item);
        },
        deleteItem(index) {

            this.items.splice(index, 1);

            this.recalculate();
        },
        changeItem(obj) {
            console.log('OBJ: ', obj);
            this.items[obj.index][obj.field] = obj.value;

            if(obj.field == 'quantity' || obj.field == 'price' || obj.field == 'discount') {
                let subTotal =  this.items[obj.index].quantity * this.items[obj.index].price; 

                let dto = subTotal * this.items[obj.index].discount / 100;

                this.items[obj.index].subtotal = subTotal - dto;

                this.recalculate();
            }
        },
        recalculate() {
            this.reset();
            this.items.forEach(elem => {
                this.subtotalFra += elem.subtotal;
                let vat = elem.subtotal * elem.vat / 100;
                this.iva += vat;
            });

            this.totalFra = this.subtotalFra + this.iva;
        },
        reset() {
            this.subtotalFra = 0;
            this.iva = 0;
            this.totalFra = 0;
        }
    }
}


</script>

<template>
    <TopBar />

    <div class="relative flex flex-col min-h-screen px-4 py-6 md:px-10 bg-slate-50 dark:bg-slate-800 font-outfit sm:py-12">
        
        <div class="md:mx-auto border border-blue-300">
            <div class="flex flex-col justify-between w-full  sm:space-x-8 lg:flex-row md:flex-col sm:space-y-0">
                <div class="p-5 bg-white max-w-7xl rounded-2xl sm:p-10 dark:bg-slate-900">
                    col 1
                    <Switch @click="toggleDark()" />

                    <div class="mt-8 mb-8 md:grid md:grid-cols-2 gap-6">
                                            
                        <div class="md:flex-col lg:flex-row">
                            <div class="font-semibold capitalize pb-2 space-x-2">Vendedor</div>
                            <CompanyData />
                        </div>

                        <div class="md:flex-col lg:flex-row">
                            <div class="font-semibold capitalize pb-2 space-x-2">Comprador</div>
                            <CompanyData />
                        </div>

                    </div>

                    <hr>

                    <div class="mt-8">
                        <div class="w-1/2">   
                            <div class="flex flex-col space-y-2 sm:w-96  ">   
                                <div class="flex flex-col justify-between text-gray-900 dark:text-white sm:items-center sm:flex-row">
                                    <div class="text-sm capitalize">Factura nº</div>
                                    <div><input placeholder="Número de factura" class="inp"></div>
                                </div>
                            </div>

                            <div class="flex flex-col space-y-4 sm:w-96  ">   
                                <div class="flex flex-col justify-between text-gray-900 dark:text-white sm:items-center sm:flex-row">
                                    <div class="text-sm capitalize">Fecha </div>
                                    <div><VueDatePicker :dark="isDark" placeholder="Fecha factura" locale="es" /></div>
                                </div>
                            </div>

                            <div class="flex flex-col space-y-4 sm:w-96  ">   
                                <div class="flex flex-col justify-between text-gray-900 dark:text-white sm:items-center sm:flex-row">
                                    <div class="text-sm capitalize">Fecha pago</div>
                                    <div><VueDatePicker :dark="isDark" placeholder="Fecha pago" locale="es" /></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="mt-8">
                        <div class=" space-y-4">
                            <div class="grid grid-cols-10 gap-1 py-2">
                                <div class="col-span-4">Concepto</div>
                                <div class="col-span-1">Cantidad</div>
                                <div class="col-span-1">Precio</div>
                                <div class="col-span-1">Dto. %</div>
                                <div class="col-span-1">IVA %</div>
                                <div class="col-span-1">Subtotal</div>
                            </div>
                        </div>
                        <InvoiceItem v-for="(item, index) in items" :key="index + 1"
                            :title="item.title"
                            :description="item.description"
                            :quantity="item.quantity"
                            :price="item.price"
                            :discount="item.discount"
                            :vat="item.vat"
                            :subtotal="item.subtotal"
                            :index="index"
                            @item-added="addItem"
                            @item-removed="deleteItem"
                            @value-changed="changeItem"
                         />

                        <div class="mt-4">
                            <div class="my-2">
                                 <div class="grid grid-cols-10 gap-1 py-2">
                                    <div class="col-span-7"></div>
                                    <div class="col-span-1">Subtotal</div>
                                    <div class="col-span-1">
                                        <input type="number" step='0.01' class="inp text-right" :value="subtotalFra">
                                    </div>
                                 </div>
                            </div>

                            <div class="my-2">
                                 <div class="grid grid-cols-10 gap-1 py-2">
                                    <div class="col-span-7"></div>
                                    <div class="col-span-1">IVA</div>
                                    <div class="col-span-1">
                                        <input type="number" step='0.01' class="inp text-right" :value="iva" >
                                    </div>
                                 </div>
                            </div>

                            <div class="my-2">
                                 <div class="grid grid-cols-10 gap-1 py-2">
                                    <div class="col-span-7"></div>
                                    <div class="col-span-1">Total</div>
                                    <div class="col-span-1">
                                        <input type="number" step='0.01' class="inp text-right" :value="totalFra" >
                                    </div>
                                 </div>
                            </div>                           
                        </div>
                        
                    </div>

                </div>

                <div class="md:mx-auto md:mt-10 p-5 bg-white sm:w-80 rounded-xl dark:bg-slate-900 dark:text-white">
                    col 2
                </div>



            </div>        
        </div>


    </div>
    


</template>

<style>
    html.dark {
        color-scheme: dark;
    }

    body {
        @apply bg-slate-50 text-slate-800 dark:bg-slate-800 dark:text-slate-50;
    }


    .inp {
        width: 100%;
        border-radius: 0.5rem;
        border-style: none;
        --tw-bg-opacity: 1;
        background-color: rgb(249 250 251 / var(--tw-bg-opacity));
        padding: 0.5rem;
        font-size: .875rem;
        line-height: 1.25rem;
    }

    .inp {
        --tw-ring-offset-shadow: var(--tw-ring-inset) 0 0 0 var(--tw-ring-offset-width) var(--tw-ring-offset-color);
        --tw-ring-shadow: var(--tw-ring-inset) 0 0 0 calc(1px + var(--tw-ring-offset-width)) var(--tw-ring-color);
        box-shadow: var(--tw-ring-offset-shadow),var(--tw-ring-shadow),var(--tw-shadow, 0 0 #0000);
        --tw-ring-opacity: 1;
        --tw-ring-color: rgb(229 231 235 / var(--tw-ring-opacity));
    }

    :is(.dark .inp) {
        --tw-bg-opacity: 1;
        background-color: rgb(30 41 59 / var(--tw-bg-opacity));
        --tw-text-opacity: 1;
        color: rgb(255 255 255 / var(--tw-text-opacity));
        --tw-ring-opacity: 1;
        --tw-ring-color: rgb(71 85 105 / var(--tw-ring-opacity));
    }

.dp__main {
    max-width: 167px;
}

.dp__theme_dark {
    --dp-background-color: rgb(30 41 59 / var(--tw-bg-opacity));
    --dp-text-color: #fff;
    --dp-hover-color: #484848;
    --dp-hover-text-color: #fff;
    --dp-hover-icon-color: #959595;
    --dp-primary-color: #005cb2;
    --dp-primary-disabled-color: #61a8ea;
    --dp-primary-text-color: #fff;
    --dp-secondary-color: #a9a9a9;
    --dp-border-color:rgb(71 85 105);
    --dp-menu-border-color: #2d2d2d;
    --dp-border-color-hover: #aaaeb7;
    --dp-disabled-color: #737373;
    --dp-disabled-color-text: #d0d0d0;
    --dp-scroll-bar-background: #212121;
    --dp-scroll-bar-color: #484848;
    --dp-success-color: #00701a;
    --dp-success-color-disabled: #428f59;
    --dp-icon-color: #959595;
    --dp-danger-color: #e53935;
    --dp-marker-color: #e53935;
    --dp-tooltip-color: #3e3e3e;
    --dp-highlight-color: rgb(0 92 178 / 20%);
    --dp-range-between-dates-background-color: var(--dp-hover-color, #484848);
    --dp-range-between-dates-text-color: var(--dp-hover-text-color, #fff);
    --dp-range-between-border-color: var(--dp-hover-color, #fff);
}

.dp__theme_light {
    --dp-background-color: rgb(249 250 251 / var(--tw-bg-opacity));
    --dp-text-color: #212121;
    --dp-hover-color: #f3f3f3;
    --dp-hover-text-color: #212121;
    --dp-hover-icon-color: #959595;
    --dp-primary-color: #1976d2;
    --dp-primary-disabled-color: #6bacea;
    --dp-primary-text-color: #f8f5f5;
    --dp-secondary-color: #c0c4cc;
    --dp-border-color: #ddd;
    --dp-menu-border-color: #ddd;
    --dp-border-color-hover: #aaaeb7;
    --dp-disabled-color: #f6f6f6;
    --dp-scroll-bar-background: #f3f3f3;
    --dp-scroll-bar-color: #959595;
    --dp-success-color: #76d275;
    --dp-success-color-disabled: #a3d9b1;
    --dp-icon-color: #959595;
    --dp-danger-color: #ff6f60;
    --dp-marker-color: #ff6f60;
    --dp-tooltip-color: #fafafa;
    --dp-disabled-color-text: #8e8e8e;
    --dp-highlight-color: rgb(25 118 210 / 10%);
    --dp-range-between-dates-background-color: var(--dp-hover-color, #f3f3f3);
    --dp-range-between-dates-text-color: var(--dp-hover-text-color, #212121);
    --dp-range-between-border-color: var(--dp-hover-color, #f3f3f3);
}


:root {
    /*General*/
    --dp-font-family: -apple-system, blinkmacsystemfont, "Segoe UI", roboto, oxygen, ubuntu, cantarell, "Open Sans",
    "Helvetica Neue", sans-serif;
    --dp-border-radius: 0.5rem; /*Configurable border-radius*/ 
    --dp-cell-border-radius: 0.5rem ; /*Specific border radius for the calendar cell*/
    --dp-common-transition: all 0.1s ease-in; /*Generic transition applied on buttons and calendar cells*/

    /*Sizing*/
    --dp-button-height: 35px; /*Size for buttons in overlays*/
    --dp-month-year-row-height: 35px; /*Height of the month-year select row*/
    --dp-month-year-row-button-size: 35px; /*Specific height for the next/previous buttons*/
    --dp-button-icon-height: 20px; /*Icon sizing in buttons*/
    --dp-cell-size: 35px; /*Width and height of calendar cell*/
    --dp-cell-padding: 5px; /*Padding in the cell*/
    --dp-common-padding: 10px; /*Common padding used*/
    --dp-input-icon-padding: 35px; /*Padding on the left side of the input if icon is present*/
    --dp-input-padding: 6px 30px 6px 12px; /*Padding in the input*/
    --dp-menu-min-width: 260px; /*Adjust the min width of the menu*/
    --dp-action-buttons-padding: 2px 5px; /*Adjust padding for the action buttons in action row*/
    --dp-row-margin:  5px 0; /*Adjust the spacing between rows in the calendar*/
    --dp-calendar-header-cell-padding:  0.5rem; /*Adjust padding in calendar header cells*/
    --dp-two-calendars-spacing:  10px; /*Space between multiple calendars*/
    --dp-overlay-col-padding:  3px; /*Padding in the overlay column*/
    --dp-time-inc-dec-button-size:  32px; /*Sizing for arrow buttons in the time picker*/
    --dp-menu-padding: 6px 8px; /*Menu padding*/
    
    /*Font sizes*/
    --dp-font-size: 1rem; /*Default font-size*/
    --dp-preview-font-size: 0.8rem; /*Font size of the date preview in the action row*/
    --dp-time-font-size: 0.8rem; /*Font size in the time picker*/
    
    /*Transitions*/
    --dp-animation-duration: 0.1s; /*Transition duration*/
    --dp-menu-appear-transition-timing: cubic-bezier(.4, 0, 1, 1); /*Timing on menu appear animation*/
    --dp-transition-timing: ease-out; /*Timing on slide animations*/
}

</style>