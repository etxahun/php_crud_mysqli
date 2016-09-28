# php_crud_mysqli
PHP CRUD Operations without Page Refresh using jQuery Ajax MySQL.

# Description
Here we’ll implement PHP CRUD operations without page refresh using jQuery, Ajax, and MySQL. The functionality of the example script would be read, add, update, and delete the records from MySQL database.

In this example script, we’ll fetch the users data from the database and display the user data list with add link, edit link, and delete link. By these links user can add new data to the database, update previously inserted data and delete the data from the database. All operations will happen on a single page without page refresh. Also, bootstrap table structure will be used for styling the list, form fields, and links.

## Database table creation
For this example script, we’ll create a simple table (users) with some basic columns where users data would be stored.

## Database Class - dbclass.php
DB class handles all the operations related to the database. For example, connect with the database, insert, update and delete record from the database. You need to change the $dbHost, $dbUsername, $dbPassword, and $dbName variables value as per the database credentials.

## User Action - userAction.php
This file handles the requests coming from the view file (index.php) and returns the respective requested data. Here the code is executed based on the action_type. action_type can be five types, data, view, add, edit, and delete. The following operations can happen based on the action_type:

* **data** returns the single user data based on the id as JSON format.
* **view** returns all the users data as HTML format.
* **add** insert the record in the database and return the status.
* **edit** updates the record in the database and returns the status.
* **delete** deletes the record from the database and returns the status.

## Index - index.php
This is the main view file which is visible to the user. In this single page, the user can do all the CRUD operations. For better understanding we’ve divided this script into two parts.

**getUsers()** function is used to get the users data from the userAction.php file using ajax and render the received HTML in the user data list:
~~~
function getUsers(){
    $.ajax({
        type: 'POST',
        url: 'userAction.php',
        data: 'action_type=view&'+$("#userForm").serialize(),
        success:function(html){
            $('#userData').html(html);
        }
    });
}`
~~~

**userAction()** function is used to send add, edit, and delete request to the userAction.php file using ajax and give the response to the user:

~~~
function userAction(type,id){
    id = (typeof id == "undefined")?'':id;
    var statusArr = {add:"added",edit:"updated",delete:"deleted"};
    var userData = '';
    if (type == 'add') {
        userData = $("#addForm").find('.form').serialize()+'&action_type='+type+'&id='+id;
    }else if (type == 'edit'){
        userData = $("#editForm").find('.form').serialize()+'&action_type='+type;
    }else{
        userData = 'action_type='+type+'&id='+id;
    }
    $.ajax({
        type: 'POST',
        url: 'userAction.php',
        data: userData,
        success:function(msg){
            if(msg == 'ok'){
                alert('User data has been '+statusArr[type]+' successfully.');
                getUsers();
                $('.form')[0].reset();
                $('.formData').slideUp();
            }else{
                alert('Some problem occurred, please try again.');
            }
        }
    });
}
~~~

**editUser()** function is used to get the particular user data from the userAction.php file and set the respective value in the edit form fields.

~~~
function editUser(id){
    $.ajax({
        type: 'POST',
        dataType:'JSON',
        url: 'userAction.php',
        data: 'action_type=data&id='+id,
        success:function(data){
            $('#idEdit').val(data.id);
            $('#nameEdit').val(data.name);
            $('#emailEdit').val(data.email);
            $('#phoneEdit').val(data.phone);
            $('#editForm').slideDown();
        }
    });
}
~~~

**HTML:**
Initially already inserted users data is fetched from the users table and listed with edit & delete links. At the top of the user list, add link would be displayed. The add and edit form HTMl would be visible if add or edit link is clicked.
