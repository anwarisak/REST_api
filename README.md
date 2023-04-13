> CRUD REST API

```

<?php

header("content-type: application/json");
include '..//config/conn.php';
// $action = $_POST['action'];

function register_order($conn)
{
    extract($_POST);
    $data = array();
    $query = "INSERT INTO orders (customer_id, item_name, size, shipping_type_id,shipment, shipping_from, shipping_to,shipped_date, planed_date)
     values('$customer_id', '$item_name', '$size', '$shipping_type_id','$shipment', '$shipping_from', '$shipping_to','$shipped_date','$planed_date')";

    $result = $conn->query($query);


    if ($result) {


        $data = array("status" => true, "data" => "Order registered has been created");
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}




function tableview($conn)
{
    $data = array();
    $array_data = array();
    $query = "select * from orderstable ";
    $result = $conn->query($query);


    if ($result) {
        while ($row = $result->fetch_assoc()) {
            $array_data[] = $row;
        }
        $data = array("status" => true, "data" => $array_data);
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}


function read_customer($conn)
{
    $data = array();
    $array_data = array();
    $query = "SELECT customer_id, concat(fristname,' ' ,lastname) as fristname from customer";
    $result = $conn->query($query);


    if ($result) {
        while ($row = $result->fetch_assoc()) {
            $array_data[] = $row;
        }
        $data = array("status" => true, "data" => $array_data);
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}

function read_shipping_type($conn)
{
    $data = array();
    $array_data = array();
    $query = "SELECT shipping_type_id, name from shipping_type";
    $result = $conn->query($query);


    if ($result) {
        while ($row = $result->fetch_assoc()) {
            $array_data[] = $row;
        }
        $data = array("status" => true, "data" => $array_data);
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}



function get_order($conn)
{
    extract($_POST);
    $data = array();
    $array_data = array();
    $query = "SELECT * FROM orders where order_id= '$order_id'";
    $result = $conn->query($query);

    if ($result) {
        $row = $result->fetch_assoc();

        $data = array("status" => true, "data" => $row);
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}




function update_order($conn)
{
    extract($_POST);

    $data = array();

    $query = "UPDATE orders set customer_id = '$customer_id',item_name = '$item_name', size = '$size',shipping_type_id = '$shipping_type_id',
    shipment = '$shipment', shipping_from= '$shipping_from', shipping_to= '$shipping_to', shipped_date= '$shipped_date', planed_date= '$planed_date' WHERE order_id = '$order_id'";


    $result = $conn->query($query);


    if ($result) {

        $data = array("status" => true, "data" => "well done");
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}

function delete_order($conn)
{
    extract($_POST);
    $data = array();
    $array_data = array();
    $query = "DELETE FROM orders where order_id= '$order_id'";
    $result = $conn->query($query);


    if ($result) {


        $data = array("status" => true, "data" => "successfully Deleted");
    } else {
        $data = array("status" => false, "data" => $conn->error);
    }

    echo json_encode($data);
}


if (isset($_POST['action'])) {
    $action = $_POST['action'];
    $action($conn);
} else {
    echo json_encode(array("status" => false, "data" => "Action Required....."));
}




```

> USER ID GENERATE

```

function register_user($conn){
    extract($_POST);
    $data = array();
    $eroor_Array= array();
    $new_id= generate($conn);

    $file_name= $_FILES['image']['name'];
    $file_type= $_FILES['image']['type'];
    $file_size= $_FILES['image']['size'];

    $save_name= $new_id . ".png";

    // allowed images

    $allowedImages =  ["image/jpg", "image/jpeg", "image/png"];
    $max_size= 5 * 1024 * 1024;

    if(in_array($file_type, $allowedImages)){

        if($file_size > $max_size){
            $eroor_Array[]=  $file_size/1024/1024 . " MB  file size must be less then" . $max_size/1024/1024 ." MB";
        }


    }else{
        $eroor_Array[]= "this file is not Allowed";
    }

     if(count($eroor_Array) <= 0){
        $query = "INSERT INTO user(user_id, username,employee_id, password, image)
        VALUES('$new_id', '$username', '$employee_id', MD5('$password'), '$save_name')";

       $result = $conn->query($query);


       if($result){
       move_uploaded_file($_FILES['image']['tmp_name'], "../uploads/" . $save_name);
       $data = array("status" => true, "data"=>  "successfully Registered");


       }else{
           $data = array("status" => false, "data"=> $conn->error);

       }


     }else{
        $data = array("status" => false, "data"=> $eroor_Array);

     }


    echo json_encode($data);
}

-- id generate

function generate($conn){
    $new_id= '';
    $data = array();
    $array_data = array();
   $query ="SELECT * FROM user order by user_id DESC limit 1";
    $result = $conn->query($query);


    if($result){
        $num_rows= $result->num_rows;

        if($num_rows > 0){
            $row = $result->fetch_assoc();

            $new_id = ++$row['user_id'];

        }else{

            $new_id= "USR001";
        }




    }else{
        $data = array("status" => false, "data"=> $conn->error);

    }

  return $new_id;
}
```
