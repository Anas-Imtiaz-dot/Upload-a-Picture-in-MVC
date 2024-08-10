# Upload-a-Picture-in-MVC
#Upload a Picture in MVC
#Add Field in Model
public string ImagePath {  get; set; } = string.Empty;

  [NotMapped]
  public  IFormFile? Image {  get; set; }

#Controller Code
   if (Image != null && Image.Length > 0)
   {
       var uploadsFolder = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/picture/Employee-Picture");
       var filePath = Path.Combine(uploadsFolder, Image.FileName);

       using (var stream = new FileStream(filePath, FileMode.Create))
       {
           Image.CopyTo(stream);
       }

       // Set the relative path to be saved in the database
       model.ImagePath = "/picture/Employee-Picture/" + Image.FileName;
   }
   #Fields adds in Form 
   <thead>
    <tr>
        <th>Employee Id</th>
         
        <th>Full Name</th>
        <th>Email</th>
        <th>Contact</th>
        <th>Address</th>
        <th>Department</th>
         <th>Photo</th>
        <th>Action</th>
        
    </tr>
</thead>
#Javascript function
 $('#btnsave').click(function () {
 event.preventDefault(); // Prevent the default form submission
     var _empID = $('#EmpId').val(); // Ensure the ID field is correct
     console.log($('#Employees-Form').serialize());
     

 var formData = new FormData($('#Employees-Form')[0]);

     $.ajax({
         type: 'POST',
         url: '@Url.Action("SaveEmployees", "Employees")',
        data: formData,
        contentType: false, // Set this to false for file uploads
         processData: false,
         success: function (response) {
             if (response.isSuccess) {
                 var data = response.data;
                 var row = `<tr>
                         <td>${data.empid}</td>
                         <td>${data.fullname}</td>
                         <td>${data.email}</td>
                         <td>${data.contact}</td>
                         <td>${data.address}</td>
                         <td>${data.departmentName}</td>
                            <td><img class="empimage" src="${data.imagepath}" alt="Employee Image" /></td>
                     <td>
                             <button type="button" class="btn btn-warning" onclick="Edit(${data.empid},this)"><i class="fa-solid fa-edit"></i></button> |
                         <button type="button" class="btn btn-danger" onclick="Delete(${data.empid},this)"><i class="fa-solid fa-trash"></i></button>
                     </td>
                 </tr>`;

                 if (_empID !== '') {
                     // Edit
                     if (currentRow) {
                         currentRow.replaceWith(row);
                     }
                 } else {
                     // Add new
                     $('#employeeListDiv').append(row);
                 }
                 notyf.open({ type: 'success', message: response.message || "Record saved successfully" });
             } else {
                 notyf.open({ type: 'error', message: response.message || "Record cannot be saved" });
             }
         },
         error: function (err) {
             console.log(err.statusText);
             notyf.open({ type: 'error', message: err.statusText });
         }
     });
 });
