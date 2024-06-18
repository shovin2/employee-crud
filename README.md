import React, { useState } from "react";
import { Modal, Button, Dropdown } from "react-bootstrap";
import "bootstrap/dist/css/bootstrap.min.css";
import "./styles.css";
import { FontAwesomeIcon } from "@fortawesome/react-fontawesome";
import {
  faEllipsisV,
  faEdit,
  faTrashAlt,
  faInfoCircle,
} from "@fortawesome/free-solid-svg-icons";

function App() {
  const [data, setData] = useState([]);
  const [depName, setDepName] = useState("");
  const [group, setGroup] = useState("");
  const [description, setDescription] = useState("");
  const [employeeName, setEmployeeName] = useState("");
  const [age, setAge] = useState("");
  const [gender, setGender] = useState("");
  const [employeeAddress, setEmployeeAddress] = useState("");
  const [phoneNumber, setPhoneNumber] = useState("");
  const [id, setId] = useState(0);
  const [isUpdate, setIsUpdate] = useState(false);
  const [IDCounter, setIDCounter] = useState(1);
  const [employeeCodeCounter, setEmployeeCodeCounter] = useState(1);
  const [showModal, setShowModal] = useState(false);
  const [modalMode, setModalMode] = useState("Create");
  const [selectedItem, setSelectedItem] = useState(null);
  const [showConfirmDelete, setShowConfirmDelete] = useState(false);
  const [deleteId, setDeleteId] = useState(null);

  const groups = [
    { name: "Product", code: "PDG" },
    { name: "Quality", code: "QA*" },
    { name: "Human Resource", code: "HR*" },
    { name: "Performance", code: "PER" },
  ];

  const departments = {
    Product: ["Research and Development", "Product Design"],
    Quality: ["Quality Assurance", "Quality Control"],
    "Human Resource": ["Recruitment", "Employee Relations"],
    Performance: ["Performance Analysis", "Performance Improvement"],
  };

  const getGroupCode = (groupName) => {
    const group = groups.find((g) => g.name === groupName);
    return group ? group.code : "";
  };

  const formatEmployeeCode = (groupCode, counter) => {
    return `EMP@${groupCode}${String(counter).padStart(3, "0")}`;
  };

  const handleEdit = (id) => {
    const item = data.find((item) => item.id === id);
    if (item) {
      setIsUpdate(true);
      setId(id);
      setDepName(item.depName);
      setGroup(item.group);
      setDescription(item.description);
      setEmployeeName(item.employeeName);
      setAge(item.age);
      setGender(item.gender);
      setEmployeeAddress(item.employeeAddress);
      setPhoneNumber(item.phoneNumber);
      setEmployeeCodeCounter(parseInt(item.employeeCode.match(/\d+$/)[0], 10));
      setModalMode("Edit");
      setShowModal(true);
    }
  };

  const handleDelete = (id) => {
    setDeleteId(id);
    setShowConfirmDelete(true);
  };

  const confirmDelete = () => {
    const updatedData = data.filter((item) => item.id !== deleteId);
    setData(updatedData);
    setShowConfirmDelete(false);
    setDeleteId(null);
  };

  const handleDetails = (id) => {
    const item = data.find((item) => item.id === id);
    if (item) {
      setSelectedItem(item);
      setModalMode("Details");
      setShowModal(true);
    }
  };

  const handleSave = (e) => {
    e.preventDefault();
    let error = "";

    const nameRegex = /^[a-zA-Z ]+$/;
    const phoneRegex = /^\d{10}$/;

    if (!depName.match(nameRegex))
      error +=
        "Department name is required and should contain only alphabets. ";

    if (!employeeName.match(nameRegex))
      error += "Employee name is required and should contain only alphabets. ";

    if (!phoneNumber.match(phoneRegex))
      error +=
        "Phone number must be exactly 11 digits and contain only numbers. ";

    if (data.some((item) => item.depName === depName && item.group === group)) {
      error +=
        "The same group name cannot be used again in the same department.";
    }

    if (error === "") {
      const groupCode = getGroupCode(group);
      const employeeCode = formatEmployeeCode(groupCode, employeeCodeCounter);

      const newObject = {
        id: IDCounter,
        depName,
        group,
        description,
        employeeName,
        age,
        gender,
        employeeAddress,
        phoneNumber,
        employeeCode,
      };

      setData([...data, newObject]);
      setIDCounter(IDCounter + 1);
      setEmployeeCodeCounter(employeeCodeCounter + 1);
      handleClose();
    } else {
      alert(error);
    }
  };

  const handleUpdate = (e) => {
    e.preventDefault();
    let error = "";

    const nameRegex = /^[a-zA-Z ]+$/;
    const phoneRegex = /^\d{11}$/;

    if (!depName.match(nameRegex))
      error +=
        "Department name is required and should contain only alphabets. ";

    if (!employeeName.match(nameRegex))
      error += "Employee name is required and should contain only alphabets. ";

    if (!phoneNumber.match(phoneRegex))
      error +=
        "Phone number must be exactly 11 digits and contain only numbers. ";

    if (
      data.some(
        (item) =>
          item.id !== id && item.depName === depName && item.group === group
      )
    ) {
      error +=
        "The same group name cannot be used again in the same department.";
    }

    if (error === "") {
      const updatedData = data.map((item) =>
        item.id === id
          ? {
              ...item,
              depName,
              group,
              description,
              employeeName,
              age,
              gender,
              employeeAddress,
              phoneNumber,
            }
          : item
      );

      setData(updatedData);
      handleClose();
    } else {
      alert(error);
    }
  };

  const handleClose = () => {
    setShowModal(false);
    setIsUpdate(false);
    setDepName("");
    setGroup("");
    setDescription("");
    setEmployeeName("");
    setAge("");
    setGender("");
    setEmployeeAddress("");
    setPhoneNumber("");
    setModalMode("Create");
  };

  const handleAddNewRow = () => {
    setShowModal(true);
    setModalMode("Create");
    setIsUpdate(false);
    setDepName("");
    setGroup("");
    setDescription("");
    setEmployeeName("");
    setAge("");
    setGender("");
    setEmployeeAddress("");
    setPhoneNumber("");
  };

  const handleGroupChange = (e) => {
    setGroup(e.target.value);
    setDepName("");
  };

  return (
    <div className="App">
      <div className="header">
        <h1>Employee Management</h1>
        <button
          className="btn btn-success add-button"
          onClick={handleAddNewRow}
        >
          +
        </button>
      </div>
      <table>
        <thead>
          <tr>
            <th>Employee Code</th>
            <th>Employee Name</th>
            <th>Age</th>
            <th>Gender</th>
            <th>Employee Address</th>
            <th>Phone Number</th>
            <th>Department Name</th>
            <th>Group</th>
            <th>Description</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          {data.map((item) => (
            <tr key={item.id}>
              <td>{item.employeeCode}</td>
              <td>{item.employeeName}</td>
              <td>{item.age}</td>
              <td>{item.gender}</td>
              <td>{item.employeeAddress}</td>
              <td>{item.phoneNumber}</td>
              <td>{item.depName}</td>
              <td>{item.group}</td>
              <td>{item.description}</td>
              <td className="actions">
                <Dropdown>
                  <Dropdown.Toggle
                    variant="secondary"
                    className="three-dot-button"
                    id={`dropdown-button-drop-${item.id}`}
                  >
                    <FontAwesomeIcon icon={faEllipsisV} />
                  </Dropdown.Toggle>
                  <Dropdown.Menu>
                    <Dropdown.Item onClick={() => handleEdit(item.id)}>
                      <FontAwesomeIcon icon={faEdit} /> Edit
                    </Dropdown.Item>
                    <Dropdown.Item onClick={() => handleDelete(item.id)}>
                      <FontAwesomeIcon icon={faTrashAlt} /> Delete
                    </Dropdown.Item>
                    <Dropdown.Item onClick={() => handleDetails(item.id)}>
                      <FontAwesomeIcon icon={faInfoCircle} /> Details
                    </Dropdown.Item>
                  </Dropdown.Menu>
                </Dropdown>
              </td>
            </tr>
          ))}
        </tbody>
      </table>

      <Modal show={showModal} onHide={handleClose}>
        <Modal.Header closeButton>
          <Modal.Title>
            {modalMode} Employee
            {modalMode === "Details" &&
              selectedItem &&
              ` (${selectedItem.employeeCode})`}
          </Modal.Title>
        </Modal.Header>
        <Modal.Body>
          {modalMode === "Details" && selectedItem ? (
            <div>
              <p>
                <strong>Employee Code:</strong> {selectedItem.employeeCode}
              </p>
              <p>
                <strong>Department Name:</strong> {selectedItem.depName}
              </p>
              <p>
                <strong>Group:</strong> {selectedItem.group}
              </p>
              <p>
                <strong>Description:</strong> {selectedItem.description}
              </p>
              <p>
                <strong>Employee Name:</strong> {selectedItem.employeeName}
              </p>
              <p>
                <strong>Age:</strong> {selectedItem.age}
              </p>
              <p>
                <strong>Gender:</strong> {selectedItem.gender}
              </p>
              <p>
                <strong>Employee Address:</strong>{" "}
                {selectedItem.employeeAddress}
              </p>
              <p>
                <strong>Phone Number:</strong> {selectedItem.phoneNumber}
              </p>
            </div>
          ) : (
            <form onSubmit={isUpdate ? handleUpdate : handleSave}>
              <label>
                Department Group:
                <select
                  value={group}
                  onChange={handleGroupChange}
                  disabled={modalMode === "Edit"}
                >
                  <option value="" disabled>
                    Select Group
                  </option>
                  {groups.map((groupItem, index) => (
                    <option key={index} value={groupItem.name}>
                      {groupItem.name}
                    </option>
                  ))}
                </select>
              </label>
              {group && (
                <label>
                  Department Name:
                  <select
                    value={depName}
                    onChange={(e) => setDepName(e.target.value)}
                    disabled={!group}
                  >
                    <option value="" disabled>
                      Select Department Name
                    </option>
                    {departments[group].map((depName, index) => (
                      <option key={index} value={depName}>
                        {depName}
                      </option>
                    ))}
                  </select>
                </label>
              )}
              <label>
                Description:
                <textarea
                  placeholder="Enter Description"
                  onChange={(e) => setDescription(e.target.value)}
                  value={description}
                />
              </label>
              <label>
                Employee Name:
                <input
                  type="text"
                  placeholder="Enter Employee Name"
                  onChange={(e) => setEmployeeName(e.target.value)}
                  value={employeeName}
                />
              </label>
              <label>
                Age:
                <input
                  type="number"
                  placeholder="Enter Age"
                  onChange={(e) => setAge(e.target.value)}
                  value={age}
                />
              </label>
              <label>
                Gender:
                <select
                  value={gender}
                  onChange={(e) => setGender(e.target.value)}
                >
                  <option value="" disabled>
                    Select Gender
                  </option>
                  <option value="Male">Male</option>
                  <option value="Female">Female</option>
                  <option value="Others">Others</option>
                </select>
              </label>
              <label>
                Employee Address:
                <textarea
                  placeholder="Enter Employee Address"
                  onChange={(e) => setEmployeeAddress(e.target.value)}
                  value={employeeAddress}
                />
              </label>
              <label>
                Phone Number:
                <input
                  type="text"
                  placeholder="Enter Phone Number"
                  onChange={(e) => setPhoneNumber(e.target.value)}
                  value={phoneNumber}
                />
              </label>
              <Button type="submit">
                {isUpdate ? "Update Employee" : "Add Employee"}
              </Button>
            </form>
          )}
        </Modal.Body>
      </Modal>

      <Modal
        show={showConfirmDelete}
        onHide={() => setShowConfirmDelete(false)}
      >
        <Modal.Header closeButton>
          <Modal.Title>Confirm Delete</Modal.Title>
        </Modal.Header>
        <Modal.Body>Are you sure you want to delete this item?</Modal.Body>
        <Modal.Footer>
          <Button
            variant="secondary"
            onClick={() => setShowConfirmDelete(false)}
          >
            Cancel
          </Button>
          <Button variant="danger" onClick={confirmDelete}>
            Delete
          </Button>
        </Modal.Footer>
      </Modal>
    </div>
  );
}

export default App;
