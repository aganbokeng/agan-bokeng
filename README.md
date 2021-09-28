# agan-bokeng
bekengl

67 lines (58 sloc)  2.58 KB
  
<?php session_start(); ?>
<?php
    require_once('inc/connection.php');
    $msg_error = false;
    $msg_sucess = false;

    if(isset($_POST['changePassword'])){
        //view admin details
        $adminId = $_SESSION['adminId'];
        //get passwords
        $currentPassword = mysqli_real_escape_string($connection,$_POST['currentPassword']);
        $newPassword = mysqli_real_escape_string($connection,$_POST['newPassword']);
        $conformNewPassword = mysqli_real_escape_string($connection,$_POST['conformNewPassword']);

        //encoding passwords
        $encoded_currentPassword = base64_encode($currentPassword);
        $encoded_newPassword = base64_encode($newPassword);
        $encoded_conformPassword = base64_encode($conformNewPassword);

        //check if the new passwords are match
        if($newPassword != $conformNewPassword){
            echo '<div role="alert">New password missmatch! please try agan.</div>';
            $msg_error = true;
        }else{
            //prepare select query
            $select_query = "SELECT * FROM tbladmin WHERE adminId='$adminId' LIMIT 1";
            $excute_select_query = mysqli_query($connection,$select_query);

            //get current password from database
            $user = mysqli_fetch_assoc($excute_select_query);
            $currentPassword_databse = $user['password'];
            
            //check user enterd password and password get in database is are semiler
            if($currentPassword_databse == $encoded_currentPassword){
                //prepare update query
                $update_query="UPDATE tbladmin SET password = '$encoded_newPassword' WHERE adminId='$adminId'";
                $excute_update_query= mysqli_query($connection,$update_query);

                //if update query is sucess
                if($excute_update_query){
                    echo 'New password update sucess!';
                    $msg_sucess = true;
                }else{
                    echo mysqli_error($connection);
                    $msg_error = true;
                }
            }else{
                echo 'Current password incorrect! please try agan.';
                $msg_error = true;
            }
            
        }
    }
?>
<script>
    var msg_error = "<?php echo $msg_error;?>";
    var msg_sucess = "<?php echo $msg_sucess;?>";
    
    if(msg_error == true){
        $("#satus_msg").addClass("alert-danger");
        $("#satus_msg").removeClass("alert-success");
    }else if(msg_sucess == true){
        $("#satus_msg").addClass("alert-success");
        $("#satus_msg").removeClass("alert-danger");
    }
    
</script>
