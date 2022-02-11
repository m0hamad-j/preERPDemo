<script>
  // Svelte imports
  
  // Nodejs install imports
  import { Datatable } from "svelte-simple-datatables";
  import { DialogOverlay, DialogContent } from "svelte-accessible-dialog";
  
  // Custom imoprts
  import { csvGenerator } from "./csvGenerator";
	import CustomMenu from './Util/Components/ContextMenu.svelte';
  

  let data = [
    {id: 1,first_name: "Hussein",last_name: "Sakr",address: "Beirut",phone: "03-757998",currency: "USD","local-import": "Local",taxable: "Yes",},
    {id: 2,first_name: "Farid",last_name: "Assi",address: "Matar Road",phone: "03-139139",currency: "LBP","local-import": "Local",taxable: "Yes",},
    {id: 3,first_name: "Karim",last_name: "Saad",address: "Hamra",phone: "03-415520",currency: "USD","local-import": "Import",taxable: "No",},
    {id: 4,first_name: "Tamer",last_name: "Bitar",address: "Hamra",phone: "03-201201",currency: "USD","local-import": "Import",taxable: "No",},
  ];

  let dialogOverlayIsOpen, rowToEdit, rows, fName, lName, address, phone, currency, taxable ;

  const openDialogOverlay = (row) => {
    dialogOverlayIsOpen = true;
    if (typeof row.id != "undefined") {
      fName = row.first_name;
      lName = row.last_name;
      address = row.address;
      phone = row.phone;
      currency = row.currency;
      taxable = row.taxable;
      rowToEdit = row;
    } else {
      fName = lName = address = phone = currency = taxable = "";
      rowToEdit = "";
    }
  };

  const closeDialogOverlay = () => {
    dialogOverlayIsOpen = false;
  };

  // Should be for specific div or talbe
  const printCurrentPage = () => {
    window.print();
  };

  function addNewUser() {
    if (typeof rowToEdit.id != "undefined")
      data[rowToEdit.id - 1] = {id: rowToEdit.id,first_name: fName,last_name: lName,address: address,phone: phone,currency: currency,"local-import": "Local",taxable: taxable};
      else
      data = [...data,{id: data.length + 1,first_name: fName,last_name: lName,address: address,phone: phone,currency: currency,"local-import": "Local",taxable: taxable}];
    dialogOverlayIsOpen = false;
  }

  function downloadHandler() {
    let tableKeys = Object.keys(data[0]); //extract key names from first Object
    let tableHeader = ["ID","First Name","Last Name","Address","Phone Number","Currency","Import/Local","Taxable",];
    csvGenerator(data, tableKeys, tableHeader, new Date()+".csv");
  }
  const settings = {
    sortable: true,
    pagination: true,
    scrollY: true,
    rowsPerPage: 10,
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
  
</script>

<main>
  <div class="tableDiv">  
    <Datatable {settings} {data} bind:dataRows={rows}>
      <table class="table" >
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
          <td><button on:click={openDialogOverlay(row)}>Edit</button></td>
        </tr>
        {/each}
        {/if}
      </tbody>
    </table>
    </Datatable>
  </div>
  <button on:click={downloadHandler}>Export to csv</button>
  <button on:click={openDialogOverlay}>Add Customer</button>
  <button on:click={printCurrentPage}>Print</button>

  <DialogOverlay isOpen={dialogOverlayIsOpen} onDismiss={closeDialogOverlay}>
    <DialogContent aria-label="Announcement">
      <input type="text" bind:value={fName} placeholder="Joe,Jay..etc" />
      <input type="text" bind:value={lName} placeholder="Denber,Ancher..etc" />
      <input type="text" bind:value={address} placeholder="Beirut" />
      <input type="text" bind:value={phone} placeholder="03-010203" />
      <input type="text" bind:value={currency} placeholder="USD-LBP..etc" />
      <input type="text" bind:value={taxable} placeholder="Yes/No" />
      <p>To implement</p>
      <button on:click={addNewUser}>Update table</button>
    </DialogContent>
  </DialogOverlay>
  <CustomMenu />
</main>

<style>
  .tableDiv {
    height: 80vh;
  }
  td{
    text-align: center;
  }
</style>
