<script setup>
import { ref, onMounted } from 'vue';
import { ethers } from 'ethers';
import { Bar } from 'vue-chartjs';
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from 'chart.js';
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

const provider = new ethers.providers.JsonRpcProvider(
  'https://rpc.callisto.network/'
);

const abi = [
  {
    type: 'function',
    stateMutability: 'view',
    outputs: [{ type: 'string', name: '', internalType: 'string' }],
    name: 'getClassPropertyForTokenID',
    inputs: [
      { type: 'uint256', name: '_tokenID', internalType: 'uint256' },
      { type: 'uint256', name: '_propertyID', internalType: 'uint256' },
    ],
  },
  {
    type: 'function',
    stateMutability: 'view',
    outputs: [{ type: 'uint256', name: '', internalType: 'uint256' }],
    name: 'next_mint_id',
    inputs: [],
  },
];
const address = '0x096194aa4dFd64b506630149B921015170753a11';
const contract = new ethers.Contract(address, abi, provider);

const getStats = async () => {
  const total = ethers.utils.formatUnits(await contract.next_mint_id(), 0);

  let getPropertyPromises = [];

  for (let index = 0; index < total; index++) {
    const nftProperty = contract.getClassPropertyForTokenID(index, 1);
    getPropertyPromises.push(nftProperty);
  }

  const nftProperties = await Promise.all(getPropertyPromises);

  const countByEdition = nftProperties.reduce((accumulator, currentValue) => {
    const edition = JSON.parse(currentValue).edition;
    accumulator[edition] = (accumulator[edition] || 0) + 1;
    return accumulator;
  }, {});

  chartData.value = {
    labels: Object.keys(countByEdition),
    datasets: [
      {
        label: 'Data One',
        backgroundColor: colors,
        data: Object.values(countByEdition),
      },
    ],
  };
};

const chartData = ref({
  datasets: [
    {
      data: [0],
    },
  ],
});

const options = {
  plugins: {
    legend: {
      display: false,
    },
  },
};

const colors = [
  '#00FFFF', // neon blue
  '#00FF8C',
  '#00FF19',
  '#28FF00',
  '#9DFF00',
  '#E3FF00',
  '#FFE300',
  '#FF7800',
  '#FF0F00', // neon green
];

// lifecycle hooks
onMounted(() => {
  getStats();
});
</script>

<template>
  <div>
    <h1>Charity</h1>
    <Bar :data="chartData" :options="options" />
  </div>
</template>
