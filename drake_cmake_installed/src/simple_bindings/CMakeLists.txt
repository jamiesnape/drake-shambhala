# -*- mode: cmake -*-
# vi: set ft=cmake :

# Copyright (c) 2018, Toyota Research Institute.
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
#
# * Neither the name of the copyright holder nor the names of its contributors
#   may be used to endorse or promote products derived from this software
#   without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.

find_package(pybind11 CONFIG REQUIRED)

pybind11_add_module(simple_bindings MODULE simple_bindings.cc)
target_link_libraries(simple_bindings PUBLIC drake::drake)
# N.B. `pybind11_add_module` normally sets the default visibility to "hidden"
# to avoid warnings. However, we need the default visibility to be public so
# template instantions that are bound in Python (e.g. `drake::Value<>`)
# maintain the same RTTI across translation units.
# Additionally, in Drake we avoid mixing hidden and public symbols, so we
# can confidently re-enable this setting for Drake's symbols.
# For more information, see:
# https://github.com/pybind/pybind11/issues/2479
set_target_properties(simple_bindings PROPERTIES CXX_VISIBILITY_PRESET default)

add_test(NAME simple_bindings_test COMMAND
    "${PYTHON_EXECUTABLE}" "${CMAKE_CURRENT_SOURCE_DIR}/simple_bindings_test.py")
set_tests_properties(simple_bindings_test PROPERTIES
    # Using `pybind11/tests/test_cmake_build/installed_target` as an example.
    ENVIRONMENT "PYTHONPATH=$<TARGET_FILE_DIR:simple_bindings>:${PYTHONPATH}")
