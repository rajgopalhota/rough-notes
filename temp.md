import React, { useState } from "react";
import { Modal, ModalHeader, ModalBody, ModalFooter, Button } from "reactstrap";
import { toast } from "react-toastify";

const images = [
  "/images/image1.jpg",
  "/images/image2.jpg",
  "/images/image3.jpg",
  "/images/image4.jpg",
  "/images/image5.jpg",
];

const ImageSelectionModal = ({ isOpen, toggle, onSelect }) => {
  const [selectedImage, setSelectedImage] = useState(null);
  const [isMinimized, setIsMinimized] = useState(false);

  const handleSubmit = () => {
    if (!selectedImage) {
      toast.error("Please select an image!");
      return;
    }
    onSelect(selectedImage);
    toast.success("Image selected successfully!");
    toggle();
  };

  return (
    <>
      {!isMinimized && (
        <Modal isOpen={isOpen} toggle={toggle} centered size="md" className="shadow-lg rounded-lg">
          <ModalHeader toggle={toggle} className="bg-primary text-white font-weight-bold d-flex justify-content-between">
            Select an Image
            <Button color="light" size="sm" className="ml-auto border" onClick={() => setIsMinimized(true)}>➖ Minimize</Button>
          </ModalHeader>

          <ModalBody className="d-flex flex-wrap justify-content-center gap-3 p-3">
            {images.map((img, index) => (
              <div 
                key={index} 
                className={`p-1 rounded cursor-pointer ${selectedImage === img ? "border border-primary shadow-sm" : "border border-light"}`} 
                onClick={() => setSelectedImage(img)}
              >
                <img 
                  src={img} 
                  alt={`Preview ${index + 1}`} 
                  className="img-fluid rounded-circle shadow-sm"
                  style={{ width: "55px", height: "55px", objectFit: "cover" }}
                />
              </div>
            ))}
          </ModalBody>

          <ModalFooter className="d-flex justify-content-between">
            <Button color="secondary" onClick={toggle}>Cancel</Button>
            <Button color="primary" onClick={handleSubmit} disabled={!selectedImage}>Submit</Button>
          </ModalFooter>
        </Modal>
      )}

      {isMinimized && (
        <div className="fixed-bottom mb-3 ml-auto mr-3 p-2 bg-white shadow-lg rounded d-flex align-items-center">
          <span className="mr-2">Image Selection Modal Minimized</span>
          <Button color="primary" size="sm" onClick={() => setIsMinimized(false)}>Restore</Button>
        </div>
      )}
    </>
  );
};

export default ImageSelectionModal;