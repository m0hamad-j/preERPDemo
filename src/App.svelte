<script>
  import { DialogOverlay, DialogContent } from "svelte-accessible-dialog";

  import { Datatable } from "svelte-simple-datatables";
  import { csvGenerator } from "./csvGenerator";

  let data = [
    {
      id: 1,
      first_name: "Hussein",
      last_name: "Sakr",
      address: "Beirut",
      phone: "03-757998",
      currency: "USD",
      "local-import": "Local",
      taxable: "Yes",
    },
    {
      id: 2,
      first_name: "Farid",
      last_name: "Assi",
      address: "Matar Road",
      phone: "03-139139",
      currency: "LBP",
      "local-import": "Local",
      taxable: "Yes",
    },
    {
      id: 3,
      first_name: "Karim",
      last_name: "Saad",
      address: "Hamra",
      phone: "03-415520",
      currency: "USD",
      "local-import": "Import",
      taxable: "No",
    },
    {
      id: 4,
      first_name: "Tamer",
      last_name: "Bitar",
      address: "Hamra",
      phone: "03-201201",
      currency: "USD",
      "local-import": "Import",
      taxable: "No",
    },
  ];
  let isOpen;
  let editRow;
  const open = (row) => {
    isOpen = true;
    if (typeof row.id != "undefined") {
      fName = row.first_name;
      lName = row.last_name;
      Address = row.address;
      Phone = row.phone;
      Currency = row.currency;
      Taxable = row.taxable;
      editRow = row;
    } else {
      fName = lName = Address = Phone = Currency = Taxable = "";
      editRow = "";
    }
  };

  const close = () => {
    isOpen = false;
  };
  const print = () => {
    window.print();
  };
  function addNewUser() {
    if (typeof editRow.id != "undefined") {
      data[editRow.id - 1] = {
        id: editRow.id,
        first_name: fName,
        last_name: lName,
        address: Address,
        phone: Phone,
        currency: Currency,
        "local-import": "Local",
        taxable: Taxable,
      };
      data = data;
    } else {
      data = [
        ...data,
        {
          id: data.length + 1,
          first_name: fName,
          last_name: lName,
          address: Address,
          phone: Phone,
          currency: Currency,
          "local-import": "Local",
          taxable: Taxable,
        },
      ];
    }
    isOpen = false;
  }

  function downloadHandler() {
    let tableKeys = Object.keys(data[0]); //extract key names from first Object
    let tableHeader = [
      "ID",
      "First Name",
      "Last Name",
      "Address",
      "Phone Number",
      "Currency",
      "Import/Local",
      "Taxable",
    ];
    csvGenerator(data, tableKeys, tableHeader, "svelte_csv_demo.csv");
  }
  const settings = {
    sortable: true,
    pagination: true,
    scrollY: true,
    rowsPerPage: 50,
    columnFilter: true,
    css: true,
    labels: {
      search: "Search...", // search input placeholer
      filter: "Filter", // filter inputs placeholder
      noRows: "No entries to found",
      info: "Showing {start} to {end} of {rows} entries",
      previous: "Previous",
      next: "Next",
    },
    blocks: {
      searchInput: true,
      paginationButtons: true,
      paginationRowCount: true,
    },
  };
  let rows;
  let fName, lName, Address, Phone, Currency, Taxable;
</script>

<main>
  <div id="main">
    <Datatable {settings} {data} bind:dataRows={rows}>
      <thead>
        <th data-key="id">ID</th>
        <th data-key="(row) => row.first_name + ' ' + row.last_name">Name</th>
        <th data-key="address">Address</th>
        <th data-key="phone">Phone Number</th>
        <th data-key="currency">Currency</th>
        <th data-key="taxable">Taxable</th>
        <th>Action</th>
      </thead>
      <tbody>
        {#if rows}
          {#each $rows as row}
            <tr>
              <td>{row.id}</td>
              <td>{row.first_name} {row.last_name}</td>
              <td>{row.address}</td>
              <td>{row.phone}</td>
              <td>{row.currency}</td>
              <td>{row.taxable}</td>
              <td><button on:click={open(row)}>Edit</button></td>
            </tr>
          {/each}
        {/if}
      </tbody>
    </Datatable>
  </div>
  <button on:click={downloadHandler}>Export to csv</button>
  <button on:click={open}>Add Customer</button>
  <button on:click={print}>Print</button>

  <DialogOverlay {isOpen} onDismiss={close}>
    <DialogContent aria-label="Announcement">
      <input type="text" bind:value={fName} placeholder="Joe,Jay..etc" />
      <input type="text" bind:value={lName} placeholder="Denber,Ancher..etc" />
      <input type="text" bind:value={Address} placeholder="Beirut" />
      <input type="text" bind:value={Phone} placeholder="03-010203" />
      <input type="text" bind:value={Currency} placeholder="USD-LBP..etc" />
      <input type="text" bind:value={Taxable} placeholder="Yes/No" />
      <p>To implement</p>
      <button on:click={addNewUser}>Update table</button>
    </DialogContent>
  </DialogOverlay>
</main>

<style>
  #main {
    height: 80vh;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  th:first-child {
    width: 72px;
  }
  td {
    text-align: center;
    padding: 4px 0;
  }
</style>
