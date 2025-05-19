<script setup>
import { ref, computed } from 'vue';
import { Head, router } from '@inertiajs/vue3';
import html2pdf from 'html2pdf.js';

const form = ref({
    from: '',
    client_name: '',
    client_email: '',
    invoice_number: '',
    issue_date: '',
    due_date: '',
    po_number: '',
    discount: 0,
    tax: 0,
    notes: '',
    terms: '',
    items: [
        { description: '', quantity: 1, unit_price: 0 }
    ]
});

const addItem = () => {
    form.value.items.push({ description: '', quantity: 1, unit_price: 0 });
};

const removeItem = (index) => {
    if (form.value.items.length > 1) {
        form.value.items.splice(index, 1);
    }
};

const subtotalAmount = computed(() => {
    return form.value.items.reduce((sum, item) => {
        return sum + (item.quantity * item.unit_price);
    }, 0).toFixed(2);
});

const totalAmount = computed(() => {
    const subtotal = parseFloat(subtotalAmount.value);
    const discount = (subtotal * (form.value.discount || 0)) / 100;
    const taxed = ((subtotal - discount) * (form.value.tax || 0)) / 100;
    return (subtotal - discount + taxed).toFixed(2);
});
const invoiceContent = ref(null);
const downloadPDF = () => {
    const element = invoiceContent.value;
    if (!element) {
        alert("Invoice content not found.");
        return;
    }

    const opt = {
        margin: 0.3,
        filename: `invoice-${form.value.invoice_number || 'untitled'}.pdf`,
        image: { type: 'jpeg', quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' }
    };

    html2pdf().set(opt).from(element).save();
};
const handleLogoUpload = (event) => {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = () => {
        form.value.logoUrl = reader.result; // Set base64 data to logoUrl
    };
    reader.readAsDataURL(file);
};


const saveDefaultCurrency = () => {
    // Save logic can be implemented
    alert(`Currency "${form.value.currency}" saved as default`);
};

// Add currency to form data
form.value.currency = 'USD ($)';


const submitInvoice = () => {
    router.post('/invoices', {
        ...form.value,
        subtotal: subtotalAmount.value,
        total: totalAmount.value,
    });
};

</script>



<template>

    <Head title="Invoice Generator - RADOSOL" />

    <div class="bg-gray-50 dark:bg-black min-h-screen text-black dark:text-white flex flex-col">

        <!-- Header -->
        <header class="bg-white dark:bg-gray-900 shadow p-4 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <img src="/images/logo.png" alt="RADOSOL Logo" class="h-10 w-10" />
                <h1 class="text-xl font-bold"><span class="text-red-600">RAD</span>O<span
                        class="text-red-600">SOL</span></h1>
            </div>
            <nav class="space-x-4 text-sm">
                <a href="#" class="hover:underline text-gray-600 dark:text-gray-300">Support</a>
                <a href="#" class="hover:underline text-gray-600 dark:text-gray-300">Docs</a>
                <a href="{{route('login')}}" class="hover:underline text-gray-600 dark:text-gray-300">Login</a>
            </nav>
        </header>

        <!-- Hero / Description Section -->
        <section class="bg-white dark:bg-black py-16 px-6 text-center">
            <div class="max-w-4xl mx-auto">
                <h2 class="text-3xl md:text-4xl font-bold text-gray-900 dark:text-white mb-4">
                    Free Invoice Template
                </h2>
                <p class="text-lg text-gray-700 dark:text-gray-300 mb-6">
                    Make beautiful Invoices with one click!
                </p>
                <p class="text-gray-600 dark:text-gray-400 leading-relaxed">
                    Welcome to the original <strong>Invoice Generator</strong>, trusted by millions of people.
                    Invoice Generator lets you instantly make invoices with our attractive invoice template straight
                    from your web browser.
                    The invoices you make can be sent and paid online or downloaded as a PDF.
                </p>
                <p class="text-gray-600 dark:text-gray-400 mt-4">
                    Did we also mention that Invoice Generator lets you generate an <strong>unlimited number of
                        invoices?</strong>
                </p>
            </div>
        </section>


        <!-- Invoice Form Section -->
        <section class="bg-white dark:bg-gray-900 py-10 px-4">

            <div class="max-w-6xl mx-auto flex flex-col md:flex-row gap-6">
                <div class="w-full md:w-4/5">
                    <!-- Top: Logo & Invoice Meta -->
                    <div  class="bg-white p-6 rounded-lg shadow">
                        <div class="flex flex-col md:flex-row justify-between items-start gap-6">

                            <!-- Logo Upload -->
                            <div class="w-full md:w-1/2">
                                <label class="block text-sm font-semibold mb-1">Add Your Logo</label>
                                <input type="file"  @change="handleLogoUpload" accept="image/*"  
                                    class="block w-full text-sm border border-dashed border-gray-400 p-4 rounded-lg" />
                            </div>

                            <!-- Invoice Info -->
                            <div class="w-full md:w-1/2 grid grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-sm font-semibold">Invoice #</label>
                                    <input v-model="form.invoice_number" type="text"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold">Date</label>
                                    <input v-model="form.issue_date" type="date"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold">Due Date</label>
                                    <input v-model="form.due_date" type="date"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold">PO Number</label>
                                    <input v-model="form.po_number" type="text"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                            </div>
                        </div>

                        <!-- From / To -->
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <div>
                                <label class="block text-sm font-semibold">Who is this from?</label>
                                <input v-model="form.from" type="text" class="w-full border rounded px-3 py-2"
                                    placeholder="Your business or name" required />
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-sm font-semibold">Bill To</label>
                                    <input v-model="form.client_name" type="text"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                                <div>
                                    <label class="block text-sm font-semibold">Ship To</label>
                                    <input v-model="form.client_email" type="text"
                                        class="w-full border rounded px-3 py-2" />
                                </div>
                            </div>
                        </div>

                        <!-- Items Table -->
                        <div class="overflow-x-auto">
                            <table
                                class="min-w-full border rounded overflow-hidden text-sm text-gray-800 dark:text-white">
                                <thead class="bg-gray-900 text-white">
                                    <tr>
                                        <th class="px-4 py-2 text-left">Item</th>
                                        <th class="px-4 py-2 text-right">Quantity</th>
                                        <th class="px-4 py-2 text-right">Rate</th>
                                        <th class="px-4 py-2 text-right">Amount</th>
                                        <th></th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="(item, index) in form.items" :key="index">
                                        <td class="px-4 py-2">
                                            <input v-model="item.description" type="text"
                                                class="w-full border rounded px-2 py-1" />
                                        </td>
                                        <td class="px-4 py-2">
                                            <input v-model.number="item.quantity" type="number"
                                                class="w-full text-right border rounded px-2 py-1" />
                                        </td>
                                        <td class="px-4 py-2">
                                            <input v-model.number="item.unit_price" type="number"
                                                class="w-full text-right border rounded px-2 py-1" />
                                        </td>
                                        <td class="px-4 py-2 text-right">
                                            ${{ (item.quantity * item.unit_price).toFixed(2) }}
                                        </td>
                                        <td class="px-2 py-2">
                                            <button @click="removeItem(index)" class="text-red-600 font-bold"
                                                v-if="form.items.length > 1">×</button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                            <button @click="addItem" class="mt-2 text-blue-600 hover:underline text-sm">+ Line
                                Item</button>
                        </div>

                        <!-- Notes and Terms -->
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <div>
                                <label class="block text-sm font-semibold">Notes</label>
                                <textarea v-model="form.notes" class="w-full border rounded px-3 py-2" rows="3"
                                    placeholder="e.g. Payment due in 15 days"></textarea>
                            </div>
                            <div>
                                <label class="block text-sm font-semibold">Terms</label>
                                <textarea v-model="form.terms" class="w-full border rounded px-3 py-2" rows="3"
                                    placeholder="Late fees, payment methods, delivery terms"></textarea>
                            </div>
                        </div>

                        <!-- Totals -->
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4 justify-end text-sm mt-8">
                            <div class="md:col-span-2"></div>
                            <div class="space-y-2">
                                <div class="flex justify-between">
                                    <span>Subtotal:</span>
                                    <span>${{ subtotalAmount }}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span>Discount (%):</span>
                                    <input v-model.number="form.discount" type="number"
                                        class="w-16 border rounded px-2 text-right" />
                                </div>
                                <div class="flex justify-between">
                                    <span>Tax (%):</span>
                                    <input v-model.number="form.tax" type="number"
                                        class="w-16 border rounded px-2 text-right" />
                                </div>
                                <div class="flex justify-between">
                                    <span>Total:</span>
                                    <span>${{ totalAmount }}</span>
                                </div>
                            </div>
                        </div>

                        <!-- Submit -->
                        <div class="text-right mt-8">
                            <button @click="submitInvoice"
                                class="bg-red-600 text-white px-6 py-2 rounded hover:bg-red-700">Submit Invoice</button>
                        </div>
                    </div>
                </div>
                <div class="w-full md:w-1/5 md:ml-6 mt-6 md:mt-0 space-y-6">
                    <!-- Download Button -->
                    <button @click="downloadPDF"
                        class="w-full bg-emerald-500 hover:bg-emerald-600 text-white font-semibold py-2 rounded">
                        ⬇️ Download
                    </button>

                    <!-- Currency Selector -->
                    <div>
                        <label class="block text-gray-600 text-sm font-medium mb-1">Currency</label>
                        <select v-model="form.currency" class="w-full border rounded px-3 py-2">
                            <option value="USD ($)">USD ($)</option>
                            <option value="EUR (€)">EUR (€)</option>
                            <option value="PKR (₨)">PKR (₨)</option>
                        </select>
                    </div>

                    <!-- Save Default -->
                    <button @click="saveDefaultCurrency" class="text-emerald-600 font-medium text-sm hover:underline">
                        Save Default
                    </button>
                </div>
            </div>
            <!-- Sidebar Section -->
            <!-- Invoice Preview Hidden -->
            <div id="invoice-preview" ref="invoiceContent" class="hidden print:block">
                <div class="p-6 border rounded-md text-sm">
                    <div class="flex justify-between">
                        <div>
                           <img :src="form.logoUrl" class="h-16" v-if="form.logoUrl" />
                            <p class="font-bold mt-2">{{ form.from }}</p>
                        </div>
                        <div class="text-right">
                            <p><strong>Invoice #:</strong> {{ form.invoice_number }}</p>
                            <p><strong>Date:</strong> {{ form.issue_date }}</p>
                            <p><strong>Due:</strong> {{ form.due_date }}</p>
                            <p><strong>PO Number:</strong> {{ form.po_number }}</p>
                        </div>
                    </div>

                    <hr class="my-4">

                    <div class="flex justify-between mb-4">
                        <div>
                            <p class="font-bold">Bill To:</p>
                            <p>{{ form.client_name }}</p>
                        </div>
                        <div>
                            <p class="font-bold">Ship To:</p>
                            <p>{{ form.client_email }}</p>
                        </div>
                    </div>

                    <table class="w-full text-left border-t border-b text-xs">
                        <thead>
                            <tr class="bg-gray-200">
                                <th class="p-2">Item</th>
                                <th class="p-2 text-right">Qty</th>
                                <th class="p-2 text-right">Rate</th>
                                <th class="p-2 text-right">Amount</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(item, index) in form.items" :key="index">
                                <td class="p-2">{{ item.description }}</td>
                                <td class="p-2 text-right">{{ item.quantity }}</td>
                                <td class="p-2 text-right">${{ item.unit_price.toFixed(2) }}</td>
                                <td class="p-2 text-right">${{ (item.quantity * item.unit_price).toFixed(2) }}</td>
                            </tr>
                        </tbody>
                    </table>

                    <div class="mt-4 text-sm text-right space-y-1">
                        <p><strong>Subtotal:</strong> ${{ subtotalAmount }}</p>
                        <p><strong>Discount:</strong> {{ form.discount }}%</p>
                        <p><strong>Tax:</strong> {{ form.tax }}%</p>
                        <p><strong>Total:</strong> ${{ totalAmount }}</p>
                    </div>

                    <div class="mt-4">
                        <p><strong>Notes:</strong></p>
                        <p>{{ form.notes }}</p>
                    </div>

                    <div class="mt-2">
                        <p><strong>Terms:</strong></p>
                        <p>{{ form.terms }}</p>
                    </div>
                </div>
            </div>

        </section>



        <!-- Footer -->
        <footer class="bg-gray-100 dark:bg-gray-900 border-t border-gray-200 dark:border-gray-700 mt-12 py-6">
            <div
                class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center text-sm text-gray-600 dark:text-gray-400 space-y-4 md:space-y-0">

                <!-- Left: Copyright -->
                <div>
                    © {{ new Date().getFullYear() }} <strong>RADOSOL</strong>. All rights reserved.
                </div>

                <!-- Middle: Links -->
                <div class="space-x-4">
                    <a href="#" class="hover:text-red-600 transition">Privacy Policy</a>
                    <a href="#" class="hover:text-red-600 transition">Terms of Service</a>
                    <a href="#" class="hover:text-red-600 transition">Contact</a>
                </div>

                <!-- Right: Social Icons -->
                <div class="flex space-x-4">
                    <a href="https://www.linkedin.com/in/ahsan-danish-061752202/" target="_blank"
                        class="hover:text-red-600 transition">
                        <i class="fab fa-linkedin text-xl"></i>
                    </a>
                    <a href="https://github.com/" target="_blank" class="hover:text-red-600 transition">
                        <i class="fab fa-github text-xl"></i>
                    </a>
                    <a href="mailto:ahsandanish.rad@gmail.com" class="hover:text-red-600 transition">
                        <i class="fas fa-envelope text-xl"></i>
                    </a>
                </div>
            </div>
        </footer>

    </div>
</template>
