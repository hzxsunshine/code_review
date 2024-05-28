# code_review
import zipfile
import io
import base64
from lxml import etree

def process_fpml_zip(fpml_zip_strings):
    """
    Process a sequence of strings representing the content of an FpML ZIP file.

    :param fpml_zip_strings: Sequence of strings (binary or base64 encoded).
    :return: List of parsed XML content as strings.
    """
    # Assuming fpml_zip_strings is a list of base64 encoded strings
    # Decode base64 strings to binary data
    binary_data = b''.join([base64.b64decode(s) for s in fpml_zip_strings])
    
    # If fpml_zip_strings are already in binary format, you can directly use:
    # binary_data = b''.join(fpml_zip_strings)

    # Use BytesIO to create a file-like object from binary data
    zip_file_like_object = io.BytesIO(binary_data)

    # Extract the ZIP file content
    with zipfile.ZipFile(zip_file_like_object, 'r') as zip_ref:
        xml_texts = []
        for file_name in zip_ref.namelist():
            if file_name.endswith('.xml'):
                with zip_ref.open(file_name) as xml_file:
                    xml_content = xml_file.read()
                    tree = etree.XML(xml_content)
                    xml_text = etree.tostring(tree, pretty_print=True, encoding='unicode')
                    xml_texts.append(xml_text)
    return xml_texts
