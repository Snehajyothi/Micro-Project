# Micro-Project
<!DOCTYPE html>
<!--
To change this license header, choose License Headers in Project Properties.
To change this template file, choose Tools | Templates
and open the template in the editor.
-->
<html>
    <head>
        <title>Bootstrap Example</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet"
              href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
        <script
        src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script
        src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
        
    </head>
    <body>
        <div class="container">
            <h2>Student Database</h2>
            <form id="empForm" method="post">
                <div class="form-group">
                    <label>Roll No:</label>
                    <input type="text" class="form-control"  id="rnn" onchange="getEmp()">
                </div>
                <div class="form-group">
                    <label>Full Name:</label>
                    <input type="text" class="form-control" id="name">
                </div>
                <div class="form-group">
                    <label>Class:</label>
                    <input type="text" class="form-control" id="cls">
                </div>
                <div class="form-group">
                    <label>Birth Date:</label> 
                    <input type="text" class="form-control" id="bd">
                </div>
                <div class="form-group">
                    <label>Address:</label>
                    <input type="text" class="form-control" id="ad">
                </div>
                <div class="form-group">
                    <label>Enrollment-Date:</label>
                    <input type="text" class="form-control" id="ed">
                </div>
                <input type="button" class="btn btn-primary" id="save" value="Save"
                       onclick="saveData()" disabled>
                <input type="button" class="btn btn-primary" id="change" value="Update"
                       onclick="changeData()" disabled>
                <input type="button" class="btn btn-primary" id="reset" value="Reset"
                       onclick="resetForm()" disabled>
            </form>
        </div>
        <script
        src="http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>
        <script>
            var jpdbBaseURL="http://api.login2explore.com:5577";
            var jpdbIRL= "/api/irl";
            var jpdbIML= "/api/iml";
            var empDBname= "STUDENT-DB";
            var empRelationName= "STUDENT-TABLE";
            var connectionToken= "90932908|-31949282322835113|90948086";
            
            $("rnn").focus();
            
            function getEmpIdASJsonObj(){
                var rnn = $("#rnn").val();
                var jsonStr={
                    id: rnn
                };
                return JSON.stringify(jsonStr);
            }
            
            function saveRecNo2LS(jsonObj){
                var lvData =JSON.parse(jsonObj.data);
                localStorage.setItem("recno",lvData.rollno);
            }
            
            function fillData(jsonObj){
                saveRecNo2LS(jsonObj);
                var record =JSON.parse(jsonObj.data).record;
                $("#name").val(record.name);
                 $("#cls").val(record.salary);
                 $("#bd").val(record.hra);
                 $("#ad").val(record.da);
                 $("#ed").val(record.deduction);
            }
            
            function getEmp(){
                var empIdJsonObj = getEmpIdASJsonObj();
                var getRequest = createGET_BY_KEYRequest(connectionToken,empDBname, empRelationName,empIdJsonObj);
                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(getRequest,jpdbBaseURL,jpdbIML );
                jQuery.ajaxSetup({async: true});
                if(resJsonObj.status === 400){
                    $("#save").prop("disabled", false);
                    $("#reset").prop("disabled", false);
                    $("#name").focus();
                    
                }else if(resJsonObj.status === 200){
                    
                    $("#rnn").prop("disabled", true);
                    fillData(resJsonObj);
                    
                    $("#change").prop("disabled", false);
                    $("#reset").prop("disabled", false);
                    $("#name").focus();
                }
            }
            
            function resetForm(){
                 
                 $("#rnn").val("");
                 $("#name").val("");
                 $("#cls").val("");
                 $("#bd").val("");
                 $("#ad").val("");
                 $("#ed").val("");
                 $("#rnn").prop("disabled",false);
                 $("#save").prop("disabled",true);
                 $("#change").prop("disabled",true);
                 $("#reset").prop("disabled",true);
                 $("#rnn").focus();
             }
             
            function validateData() {
               var rnn = $("#rnn").val();
                if (rnn === "") {
                   alert("Student Roll no is Required Value");
                   $("#rnn").focus();
                  return "";
                }
                var name = $("#name").val();
                  if (name === "") {
                     alert("Student Name is Required Value");
                       $("#name").focus();
                      return "";
                  }
                 var cls = $("#cls").val();
                      if (cls === "") {
                            alert("Student class is Required Value");
                              $("#cls").focus();
                                 return "";
                       }
                 var bd = $("#bd").val();
                  if (bd === "") {
                     alert("Student birth date is Required Value");
                       $("#bd").focus();
                      return "";
                  }
                  
                 var ad = $("#ad").val();
                  if (ad === "") {
                     alert("Student Address is Required Value");
                       $("#ad").focus();
                      return "";
                  }
                  
                 var ed = $("#ed").val();
                  if (ed === "") {
                      alert("Student Enrollment Date is Required Value");
                       $("#ed").focus();
                      return "";
                  }
                 var jsonStrObj = {
                    rollno: rnn,
                    name: name,
                    class: cls,
                    birthdate: bd,
                    address: ad,
                    enrollmentdate: ed
                 };
                 return JSON.stringify(jsonStrObj);
            }
            
             
            function saveData(){
                 var jsonStrObj = validateData();
                 if (jsonStrObj === "") {
                     return "";
                  }
                var putRequest = createPUTRequest(connectionToken,jsonStrObj,empDBname,empRelationName );
                alert(putRequest);
                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(putRequest,jpdbBaseURL,jpdbIML );
                jQuery.ajaxSetup({async: true});
                alert(JSON.stringify(resJsonObj));
                resetForm();
                $("rnn").focus();
            }
            
            function changeData(){
                $("#change").prop("disabled",true);
                jsonChg = validateData();
                var updateRequest = createUPDATERecordRequest(connectionToken,jsonChg,empDBname,empRelationName,localStorage.getItem("recno"));
                alert(updateRequest);
                jQuery.ajaxSetup({async: false});
                var resJsonObj = executeCommandAtGivenBaseUrl(updateRequest,jpdbBaseURL,jpdbIML );
                jQuery.ajaxSetup({async: true});
                alert(JSON.stringify(resJsonObj));
                resetForm();
                $("rnn").focus();
            }
            
          </script>
    </body>
</html>
